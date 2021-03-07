---
title: Speech szintézis Markup Language (SSML) – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: A Speech szintézis Markup Language használatával vezérelheti a kiejtési és a prosody szöveget a beszédben.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: trbye
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: d5cf5cd4c2667af3064b74ab2b1c5cd5bd347446
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102434287"
---
# <a name="improve-synthesis-with-speech-synthesis-markup-language-ssml"></a>A szintézis fejlesztése a Speech szintézis Markup Language (SSML) nyelvvel

A Speech szintézis Markup Language (SSML) egy XML-alapú Markup Language, amely lehetővé teszi a fejlesztők számára, hogy a szöveg-beszéd szolgáltatás segítségével hogyan konvertálja a bemeneti szöveget a szintetizált beszédbe. Az egyszerű szöveghez képest a SSML lehetővé teszi a fejlesztők számára a hangfelvétel, a kiejtés, a beszéd arány, a kötet és még több szöveg-beszéd típusú kimenet finomhangolását. Normál írásjelek, például egy időszak utáni felfüggesztés vagy a megfelelő hanglejtés használata, ha a mondatot kérdőjeltel végződik, automatikusan kezeli a rendszer.

A SSML beszédfelismerési szolgáltatásának megvalósítása a World Wide Web Consortium [Speech szintézis Markup Language 1,0-es verzióján](https://www.w3.org/TR/speech-synthesis)alapul.

> [!IMPORTANT]
> A kínai, Japán és koreai karakterek két karakterből állnak a számlázáshoz. További információ: [díjszabás](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

## <a name="standard-neural-and-custom-voices"></a>Standard, neurális és egyéni hangok

Válasszon a standard és a neurális hangok közül, vagy hozzon létre saját egyéni hangját a termék vagy a márka számára. a 75 + standard hangok több mint 45 nyelven és területi beállításban érhetők el, és az 5 neurális hang négy nyelven és területi beállításban érhető el. A támogatott nyelvek, területi beállítások és hangok (neurális és standard) teljes listáját a [nyelvi támogatás](language-support.md)című témakörben tekintheti meg.

A standard, a neurális és az egyéni hangok megismeréséhez lásd a [szöveg – beszéd áttekintést](text-to-speech.md).


> [!NOTE]
> A hangok különböző stílusokban és [a Text to speech oldalról](https://azure.microsoft.com/services/cognitive-services/text-to-speech/#features)történő olvasásával is hallhatók.


## <a name="special-characters"></a>Speciális karakterek

A SSML használata során vegye figyelembe, hogy a speciális karaktereket, például az idézőjeleket, az aposztrófokat és a zárójeleket el kell kerülni. További információ [: Extensible Markup Language (XML) 1,0: D függelék](https://www.w3.org/TR/xml/#sec-entexpand).

## <a name="supported-ssml-elements"></a>Támogatott SSML elemek

Minden SSML-dokumentum SSML elemekkel (vagy címkékkel) jön létre. Ezek az elemek a Pitch, a prosody, a Volume és más beállítások módosítására szolgálnak. A következő szakaszokban részletesen ismertetjük az egyes elemek használatát, és ha egy elem megadása kötelező vagy nem kötelező.  

> [!IMPORTANT]
> Ne feledje, hogy idézőjelek között idézőjeleket kell használni. A jól formázott, érvényes XML-fájlokra vonatkozó szabványokhoz idézőjelek közé kell foglalni az attribútumot. Például `<prosody volume="90">` egy jól formázott, érvényes elem, de `<prosody volume=90>` nem. Előfordulhat, hogy a SSML nem ismeri fel az idézőjelek között lévő attribútum-értékeket.

## <a name="create-an-ssml-document"></a>SSML-dokumentum létrehozása

`speak` a gyökérelem, és minden SSML-dokumentum esetében **kötelező** . Az `speak` elem fontos információkat tartalmaz, például a verziót, a nyelvet és a Markup szókincs definícióját.

**Syntax**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**Attribútumok**

| Attribútum | Leírás | Kötelező/nem kötelező |
|-----------|-------------|---------------------|
| `version` | Megadja a dokumentum korrektúrájának értelmezéséhez használt SSML-specifikáció verzióját. Az aktuális verzió 1,0. | Kötelező |
| `xml:lang` | Megadja a legfelső szintű dokumentum nyelvét. Az érték tartalmazhat kisbetűs, kétbetűs nyelvi kódot (például: `en` ), illetve a nyelvi kódot és a nagybetűs országot/régiót (például: `en-US` ). | Kötelező |
| `xmlns` | Megadja a dokumentumhoz tartozó URI-t, amely meghatározza a SSML dokumentum Markup szókincsét (az elemek típusát és az attribútum nevét). Az aktuális URI: http://www.w3.org/2001/10/synthesis . | Kötelező |

## <a name="choose-a-voice-for-text-to-speech"></a>Hang kiválasztása szöveg és beszéd között

Az `voice` elem megadása kötelező. A szövegről beszédre használt hang megadására szolgál.

**Syntax**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**Attribútumok**

| Attribútum | Leírás | Kötelező/nem kötelező |
|-----------|-------------|---------------------|
| `name` | A szöveg-beszéd kimenethez használt hang azonosítása. A támogatott hangok teljes listáját a [nyelvi támogatás](language-support.md#text-to-speech)című témakörben tekintheti meg. | Kötelező |

**Példa**

> [!NOTE]
> Ez a példa a `en-US-AriaRUS` hangot használja. A támogatott hangok teljes listáját a [nyelvi támogatás](language-support.md#text-to-speech)című témakörben tekintheti meg.

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>Több hang használata

A `speak` elemen belül több hang is megadható a szöveg – beszéd kimenethez. Ezeket a hangokat különböző nyelveken lehet megtekinteni. Minden hang esetében a szöveget egy elembe kell becsomagolni `voice` . 

**Attribútumok**

| Attribútum | Leírás | Kötelező/nem kötelező |
|-----------|-------------|---------------------|
| `name` | A szöveg-beszéd kimenethez használt hang azonosítása. A támogatott hangok teljes listáját a [nyelvi támogatás](language-support.md#text-to-speech)című témakörben tekintheti meg. | Kötelező |

> [!IMPORTANT]
> Több hang nem kompatibilis a szó határával. Több hang használata érdekében le kell tiltani a szó határát.

### <a name="disable-word-boundary"></a>Szó határának letiltása

A Speech SDK nyelvétől függően a `"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"` tulajdonságot `false` az objektum egy példányán kell beállítania `SpeechConfig` .

# <a name="c"></a>[C#](#tab/csharp)

További információ: <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.setproperty" target="_blank"> `SetProperty` </a>.

```csharp
speechConfig.SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="c"></a>[C++](#tab/cpp)

További információ: <a href="https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setproperty" target="_blank"> `SetProperty` </a>.

```cpp
speechConfig->SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="java"></a>[Java](#tab/java)

További információ: <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setproperty#com_microsoft_cognitiveservices_speech_SpeechConfig_setProperty_String_String_" target="_blank"> `setProperty` </a>.

```java
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="python"></a>[Python](#tab/python)

További információ: <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#set-property-by-name-property-name--str--value--str-" target="_blank"> `set_property_by_name` </a>.

```python
speech_config.set_property_by_name(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

További információ: <a href="https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#setproperty-string--string-" target="_blank"> `setProperty` </a>.

```javascript
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="objective-c"></a>[Objective-C](#tab/objectivec)

További információ: <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` </a>.

```objectivec
[speechConfig setPropertyTo:@"false" byName:@"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"];
```

# <a name="swift"></a>[Swift](#tab/swift)

További információ: <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` </a>.

```swift
speechConfig!.setPropertyTo(
    "false", byName: "SpeechServiceResponse_Synthesis_WordBoundaryEnabled")
```

---

**Példa**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        Good morning!
    </voice>
    <voice name="en-US-Guy24kRUS">
        Good morning to you too Aria!
    </voice>
</speak>
```

## <a name="adjust-speaking-styles"></a>Beszéd stílusainak módosítása

> [!IMPORTANT]
> A beszélő stílusok beállítása csak neurális hangokat fog működni.

Alapértelmezés szerint a Text-to-speech szolgáltatás a standard és a neurális hangok semleges beszéd stílusa alapján szintetizálja a szöveget. Az neurális hangok segítségével úgy állíthatja be a beszéd stílusát, hogy a különböző érzelmeket, például a vidámságot, az empátiát és a nyugalmat, vagy optimalizálja a hangját a különböző forgatókönyvekhez, például az ügyfélszolgálathoz, a bemondáshoz és a hangsegédhez a `mstts:express-as` elem használatával. Ez egy opcionális elem, amely egyedi a beszédfelismerési szolgáltatáshoz.

A jelen neurális hangok esetében jelenleg a beszélő stílusának módosításait támogatja a rendszer:
* `en-US-AriaNeural`
* `en-US-JennyNeural`
* `en-US-GuyNeural`
* `pt-BR-FranciscaNeural`
* `zh-CN-XiaoxiaoNeural`
* `zh-CN-YunyangNeural`
* `zh-CN-YunyeNeural`
* `zh-CN-YunxiNeural` Előnézet
* `zh-CN-XiaohanNeural` Előnézet
* `zh-CN-XiaomoNeural` Előnézet
* `zh-CN-XiaoxuanNeural` Előnézet
* `zh-CN-XiaoruiNeural` Előnézet

A beszélő stílus intenzitása tovább módosítható, hogy jobban illeszkedjen a használati esethez. Megadhat egy erősebb vagy lágyabb stílust, `styledegree` hogy a beszéd jobban kifejező vagy visszafogott legyen. 

A jelen neurális hangok esetében jelenleg a beszélő stílusának módosításait támogatja a rendszer:
* `zh-CN-XiaoxiaoNeural`

A beszélő stílusok és a stílus mértékének módosítása mellett a paramétert úgy is beállíthatja, `role` hogy a hang egy másik kort és nemre is utánozza. Például egy férfi hangja növelheti a pályát, és megváltoztathatja a hanglejtést, hogy utánozza a női hangokat.

Az alábbi neurális hangok esetében jelenleg támogatott a szerepkör-lejátszási beállítások használata:
* `zh-CN-XiaomoNeural`
* `zh-CN-XiaoxuanNeural`

A fenti módosítások a mondat szintjén lesznek alkalmazva, a stílusok és a szerepkör pedig hangon is változhat. Ha a stílus vagy a szerepkör lejátszása nem támogatott, a szolgáltatás a beszédet az alapértelmezett semleges beszéd módon fogja visszaadni. A [hanglista API](rest-text-to-speech.md#get-a-list-of-voices) -n keresztül, illetve a kód nélküli [hangtartalom-létrehozási](https://aka.ms/audiocontentcreation) platformon megtekintheti, hogy milyen stílusok és szerepjátékok támogatottak minden hangon.

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
> Jelenleg csak a következőt `styledegree` támogatja: zh-CN-XiaoxiaoNeural. `role` csak a zh-CN-XiaomoNeural és a zh-CN-XiaoxuanNeural használatát támogatja.

**Attribútumok**

| Attribútum | Leírás | Kötelező/nem kötelező |
|-----------|-------------|---------------------|
| `style` | Megadja a beszéd stílusát. A beszélő stílusok jelenleg hangspecifikusak. | Akkor szükséges, ha a beszélő stílust módosítja egy neurális hanghoz. A használatakor `mstts:express-as` meg kell adni a stílust. Ha a megadott érték érvénytelen, akkor a rendszer figyelmen kívül hagyja ezt az elemet. |
| `styledegree` | Megadja a beszélő stílusának intenzitását. **Elfogadott értékek**: 0,01 – 2. Az alapértelmezett érték 1, ami az előre definiált stílus intenzitását jelenti. A minimális egység 0,01, ami némileg hajlamos a cél stílusára. A 2 érték a stílus alapértelmezett intenzitásának megkettőzését eredményezi.  | Opcionális (jelenleg csak a következőt `styledegree` támogatja: zh-CN-XiaoxiaoNeural.)|
| `role` | Megadja a beszélő szerepkört – lejátszás. A hang más korban és nemre is reagál.  | Opcionális (jelenleg csak a következőt `role` támogatja: zh-CN-XiaomoNeural és zh-CN-XiaoxuanNeural.)|

Ebből a táblázatból megállapíthatja, hogy az egyes neurális hangfelismerések milyen beszélő stílusokat támogatnak.

| Hang                   | Stílus                     | Description                                                 |
|-------------------------|---------------------------|-------------------------------------------------------------|
| `en-US-AriaNeural`      | `style="newscast-formal"` | Formális, magabiztos és mérvadó hangvételt ad a hírek kézbesítéséhez |
|                         | `style="newscast-casual"` | Sokoldalú és hétköznapi hangvételt nyújt az általános hírekhez        |
|                         | `style="customerservice"` | Felhasználóbarát és hasznos hangvételt biztosít az ügyfélszolgálat számára  |
|                         | `style="chat"`            | Egy alkalmi és nyugodt hangvételt ad                         |
|                         | `style="cheerful"`        | Pozitív és boldog hangjelzést ad                         |
|                         | `style="empathetic"`      | A gondozás és a megértés értelme               |
| `en-US-JennyNeural`     | `style="customerservice"` | Felhasználóbarát és hasznos hangvételt biztosít az ügyfélszolgálat számára  |
|                         | `style="chat"`            | Egy alkalmi és nyugodt hangvételt ad                         |
|                         | `style="assistant"`       | A digitális asszisztensek meleg és nyugodt tónusa    |
|                         | `style="newscast"`        | Sokoldalú és hétköznapi hangvételt nyújt az általános hírekhez   |
| `en-US-GuyNeural`       | `style="newscast"`        | Formális és professzionális hangvételt biztosít a hírek elbeszéléséhez |
| `pt-BR-FranciscaNeural` | `style="calm"`            | Egy ritka elérésű, összegyűjtött és álló hozzáállást fejez ki beszéd közben. A Tone, a Pitch, a prosody sokkal homogénebb más típusú beszédekhez képest.                                |
| `zh-CN-XiaoxiaoNeural`  | `style="newscast"`        | Formális és professzionális hangvételt biztosít a hírek elbeszéléséhez |
|                         | `style="customerservice"` | Felhasználóbarát és hasznos hangvételt biztosít az ügyfélszolgálat számára  |
|                         | `style="assistant"`       | A digitális asszisztensek meleg és nyugodt tónusa    |
|                         | `style="chat"`            | Alkalmi és nyugodt hangvételt biztosít a Chit-csevegéshez           |
|                         | `style="calm"`            | Egy ritka elérésű, összegyűjtött és álló hozzáállást fejez ki beszéd közben. A Tone, a Pitch, a prosody sokkal homogénebb más típusú beszédekhez képest.                                |
|                         | `style="cheerful"`        | Optimista és lelkes hangvételt biztosít, amely nagyobb hangvételt és hangmagasság                         |
|                         | `style="sad"`             | Szomorú hangvételt, nagyobb hangvételt, kevesebb intenzitást és vokális energiát biztosít. Az érzelem gyakori mutatói whimpers vagy sírást okoznak a beszéd során.            |
|                         | `style="angry"`           | Egy mérges és bosszús hang, amely alacsonyabb hangvételt, nagyobb intenzitást és hangfrekvenciás energiát mutat. A beszélő olyan állapotban van, hogy dühös, nem kívánt és megsértett.       |
|                         | `style="fearful"`         | Egy megrémült és ideges hangvételt biztosít, amely nagyobb hangvételt, magasabb hangfrekvenciát és gyorsabb hangarányt biztosít. A beszélő a feszültség és a nyugtalanság állapotában van.                          |
|                         | `style="disgruntled"`     | Megvető és panaszos hangot ad. Ennek az érzelemnek a beszéde a nem élvezetet és a megvetést mutatja be.              |
|                         | `style="serious"`         | Egy szigorú és egy parancsra vonatkozó hangjelzést ad. A beszélő gyakran merevebb és sokkal kevésbé jól hangzik.          |
|                         | `style="affectionate"`    | Meleg és szeretetteljes hangvételt biztosít, amely nagyobb hangvételt és hangvételt biztosít. A beszélő olyan állapotban van, amely vonzza a figyelő figyelmét. A beszélő "személyisége" gyakran megnyerő jellegű.          |     
|                         | `style="gentle"`          | Enyhe, udvarias és kellemes hangvételt biztosít, amely az alsó és a vokális energia         |   
|                         | `style="lyrical"`         | Az érzelmeket dallamos és szentimentális módon fejezi ki         |   
| `zh-CN-YunyangNeural`   | `style="customerservice"` | Felhasználóbarát és hasznos hangvételt biztosít az ügyfélszolgálat számára  | 
| `zh-CN-YunyeNeural`     | `style="calm"`            | Egy ritka elérésű, összegyűjtött és álló hozzáállást fejez ki beszéd közben. A Tone, a Pitch, a prosody sokkal homogénebb más típusú beszédekhez képest.    | 
|                         | `style="cheerful"`        | Optimista és lelkes hangvételt biztosít, amely nagyobb hangvételt és hangmagasság                         |
|                         | `style="sad"`             | Szomorú hangvételt, nagyobb hangvételt, kevesebb intenzitást és vokális energiát biztosít. Az érzelem gyakori mutatói whimpers vagy sírást okoznak a beszéd során.            |
|                         | `style="angry"`           | Egy mérges és bosszús hang, amely alacsonyabb hangvételt, nagyobb intenzitást és hangfrekvenciás energiát mutat. A beszélő olyan állapotban van, hogy dühös, nem kívánt és megsértett.       |
|                         | `style="fearful"`         | Egy megrémült és ideges hangvételt biztosít, amely nagyobb hangvételt, magasabb hangfrekvenciát és gyorsabb hangarányt biztosít. A beszélő a feszültség és a nyugtalanság állapotában van.                          |
|                         | `style="disgruntled"`     | Megvető és panaszos hangot ad. Ennek az érzelemnek a beszéde a nem élvezetet és a megvetést mutatja be.              |
|                         | `style="serious"`         | Egy szigorú és egy parancsra vonatkozó hangjelzést ad. A beszélő gyakran merevebb és sokkal kevésbé jól hangzik.          |
| `zh-CN-YunxiNeural`     | `style="cheerful"`        | Optimista és lelkes hangvételt biztosít, amely nagyobb hangvételt és hangmagasság                         |
|                         | `style="sad"`             | Szomorú hangvételt, nagyobb hangvételt, kevesebb intenzitást és vokális energiát biztosít. Az érzelem gyakori mutatói whimpers vagy sírást okoznak a beszéd során.            |
|                         | `style="angry"`           | Egy mérges és bosszús hang, amely alacsonyabb hangvételt, nagyobb intenzitást és hangfrekvenciás energiát mutat. A beszélő olyan állapotban van, hogy dühös, nem kívánt és megsértett.       |
|                         | `style="fearful"`         | Egy megrémült és ideges hangvételt biztosít, amely nagyobb hangvételt, magasabb hangfrekvenciát és gyorsabb hangarányt biztosít. A beszélő a feszültség és a nyugtalanság állapotában van.                          |
|                         | `style="disgruntled"`     | Megvető és panaszos hangot ad. Ennek az érzelemnek a beszéde a nem élvezetet és a megvetést mutatja be.              |
|                         | `style="serious"`         | Egy szigorú és egy parancsra vonatkozó hangjelzést ad. A beszélő gyakran merevebb és sokkal kevésbé jól hangzik.    |
|                         | `style="depressed"`       | Szomorú és csüggedt hangvételt biztosít az alsó és az energia    |
|                         | `style="embarrassed"`     | Bizonytalan és tétova hangot ad, ha a beszélő kényelmetlenül érzi magát   |
| `zh-CN-XiaohanNeural`   | `style="cheerful"`        | Optimista és lelkes hangvételt biztosít, amely nagyobb hangvételt és hangmagasság                         |
|                         | `style="sad"`             | Szomorú hangvételt, nagyobb hangvételt, kevesebb intenzitást és vokális energiát biztosít. Az érzelem gyakori mutatói whimpers vagy sírást okoznak a beszéd során.            |
|                         | `style="angry"`           | Egy mérges és bosszús hang, amely alacsonyabb hangvételt, nagyobb intenzitást és hangfrekvenciás energiát mutat. A beszélő olyan állapotban van, hogy dühös, nem kívánt és megsértett.       |
|                         | `style="fearful"`         | Egy megrémült és ideges hangvételt biztosít, amely nagyobb hangvételt, magasabb hangfrekvenciát és gyorsabb hangarányt biztosít. A beszélő a feszültség és a nyugtalanság állapotában van.                          |
|                         | `style="disgruntled"`     | Megvető és panaszos hangot ad. Ennek az érzelemnek a beszéde a nem élvezetet és a megvetést mutatja be.              |
|                         | `style="serious"`         | Egy szigorú és egy parancsra vonatkozó hangjelzést ad. A beszélő gyakran merevebb és sokkal kevésbé jól hangzik.    |
|                         | `style="embarrassed"`     | Bizonytalan és tétova hangot ad, ha a beszélő kényelmetlenül érzi magát   |
|                         | `style="affectionate"`    | Meleg és szeretetteljes hangvételt biztosít, amely nagyobb hangvételt és hangvételt biztosít. A beszélő olyan állapotban van, amely vonzza a figyelő figyelmét. A beszélő "személyisége" gyakran megnyerő jellegű.          |     
|                         | `style="gentle"`          | Enyhe, udvarias és kellemes hangvételt biztosít, amely az alsó és a vokális energia         |   
| `zh-CN-XiaomoNeural`    | `style="cheerful"`        | Optimista és lelkes hangvételt biztosít, amely nagyobb hangvételt és hangmagasság                         |
|                         | `style="angry"`           | Egy mérges és bosszús hang, amely alacsonyabb hangvételt, nagyobb intenzitást és hangfrekvenciás energiát mutat. A beszélő olyan állapotban van, hogy dühös, nem kívánt és megsértett.       |
|                         | `style="fearful"`         | Egy megrémült és ideges hangvételt biztosít, amely nagyobb hangvételt, magasabb hangfrekvenciát és gyorsabb hangarányt biztosít. A beszélő a feszültség és a nyugtalanság állapotában van.                          |
|                         | `style="disgruntled"`     | Megvető és panaszos hangot ad. Ennek az érzelemnek a beszéde a nem élvezetet és a megvetést mutatja be.              |
|                         | `style="serious"`         | Egy szigorú és egy parancsra vonatkozó hangjelzést ad. A beszélő gyakran merevebb és sokkal kevésbé jól hangzik.    |
|                         | `style="depressed"`       | Szomorú és csüggedt hangvételt biztosít az alsó és az energia    |
|                         | `style="gentle"`          | Enyhe, udvarias és kellemes hangvételt biztosít, amely az alsó és a vokális energia         |  
| `zh-CN-XiaoxuanNeural`  | `style="cheerful"`        | Optimista és lelkes hangvételt biztosít, amely nagyobb hangvételt és hangmagasság                         |
|                         | `style="angry"`           | Egy mérges és bosszús hang, amely alacsonyabb hangvételt, nagyobb intenzitást és hangfrekvenciás energiát mutat. A beszélő olyan állapotban van, hogy dühös, nem kívánt és megsértett.       |
|                         | `style="fearful"`         | Egy megrémült és ideges hangvételt biztosít, amely nagyobb hangvételt, magasabb hangfrekvenciát és gyorsabb hangarányt biztosít. A beszélő a feszültség és a nyugtalanság állapotában van.                          |
|                         | `style="disgruntled"`     | Megvető és panaszos hangot ad. Ennek az érzelemnek a beszéde a nem élvezetet és a megvetést mutatja be.              |
|                         | `style="serious"`         | Egy szigorú és egy parancsra vonatkozó hangjelzést ad. A beszélő gyakran merevebb és sokkal kevésbé jól hangzik.    |
|                         | `style="depressed"`       | Szomorú és csüggedt hangvételt biztosít az alsó és az energia    |
|                         | `style="gentle"`          | Enyhe, udvarias és kellemes hangvételt biztosít, amely az alsó és a vokális energia         |   
| `zh-CN-XiaoruiNeural`    | `style="sad"`             | Szomorú hangvételt, nagyobb hangvételt, kevesebb intenzitást és vokális energiát biztosít. Az érzelem gyakori mutatói whimpers vagy sírást okoznak a beszéd során.            |
|                         | `style="angry"`           | Egy mérges és bosszús hang, amely alacsonyabb hangvételt, nagyobb intenzitást és hangfrekvenciás energiát mutat. A beszélő olyan állapotban van, hogy dühös, nem kívánt és megsértett.       |
|                         | `style="fearful"`         | Egy megrémült és ideges hangvételt biztosít, amely nagyobb hangvételt, magasabb hangfrekvenciát és gyorsabb hangarányt biztosít. A beszélő a feszültség és a nyugtalanság állapotában van.                          |

A táblázat segítségével meghatározhatja, hogy mely szerepkörök támogatottak az egyes neurális hangokon.

| Hang                   | Szerepkör                       | Leírás                                                 |
|-------------------------|----------------------------|-------------------------------------------------------------|
| `zh-CN-XiaomoNeural`    | `role="YoungAdultFemale"`  | A hang egy fiatal felnőtt nő számára utánozza.                 |
|                         | `role="OlderAdultMale"`    | A hang egy régebbi felnőtt férfit utánoz.                   |
|                         | `role="Girl"`              | A hang utánozza a lányt.                               |
|                         | `role="Boy"`               | A hang egy fiút utánoz.                                |
| `zh-CN-XiaoxuanNeural`  | `role="YoungAdultFemale"`  | A hang egy fiatal felnőtt nő számára utánozza.                 |
|                         | `role="OlderAdultFemale"`  | A hang egy régebbi felnőtt nőstényt utánoz.                 |
|                         | `role="OlderAdultMale"`    | A hang egy régebbi felnőtt férfit utánoz.                   |

**Példa**

Ez a SSML-kódrészlet azt szemlélteti, hogy az `<mstts:express-as>` elem hogyan változtatja meg a beszéd stílusát a alkalmazásban `cheerful` .

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

Ez a SSML-kódrészlet azt szemlélteti, `styledegree` hogy az attribútum hogyan módosítható a XiaoxiaoNeural beszélő stílusának intenzitása alapján.
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

Ez a SSML-kódrészlet azt szemlélteti, hogy az `role` attribútum hogyan használható a XiaomoNeural szerepkör-Play módosítására.
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

## <a name="add-or-remove-a-breakpause"></a>Szünet/szünet hozzáadása vagy eltávolítása

Az `break` elem használatával szüneteltetheti a szavak közötti szüneteltetéseket (vagy megszakításokat), vagy megakadályozhatja, hogy a szöveg-beszéd szolgáltatás automatikusan hozzáadja a szüneteltetéseket.

> [!NOTE]
> Ezzel az elemmel felülbírálhatja egy szó vagy kifejezés szöveg-beszédre (TTS) vonatkozó alapértelmezett viselkedését, ha az adott szó vagy kifejezés szintetizált beszéde természetellenesen hangzik. Állítsa a értékre `strength` `none` , hogy megakadályozza a prosodic-töréspontot, amelyet a rendszer automatikusan beszúr a szöveg-beszéd szolgáltatásba.

**Syntax**

```xml
<break strength="string" />
<break time="string" />
```

**Attribútumok**

| Attribútum | Leírás | Kötelező/nem kötelező |
|-----------|-------------|---------------------|
| `strength` | Meghatározza a Szüneteltetés relatív időtartamát az alábbi értékek egyikének használatával:<ul><li>Nincs</li><li>x – gyenge</li><li>gyenge</li><li>közepes (alapértelmezett)</li><li>erős</li><li>x – erős</li></ul> | Választható |
| `time` | Megadja a szünet időtartamát másodpercben vagy ezredmásodpercben, ez az érték kisebb, mint 5000ms. Példák érvényes értékekre, `2s` és `500ms` | Választható |

| Erősségét                      | Description |
|-------------------------------|-------------|
| Nincs, vagy ha nincs megadva érték | 0 MS        |
| x – gyenge                        | 250 MS      |
| gyenge                          | 500 ms      |
| közepes                        | 750 ms      |
| erős                        | 1000 MS     |
| x – erős                      | 1250 MS     |

**Példa**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```
## <a name="add-silence"></a>Csend hozzáadása

Az `mstts:silence` elem használatával szüneteltetheti a szüneteltetéseket szöveg előtt vagy után, vagy a 2 szomszédos mondat között. 

> [!NOTE]
>A és a közötti különbség a `mstts:silence` `break` `break` szöveg bármely helyére felvehető, de a csend csak a bemeneti szöveg elején vagy végén, vagy a két szomszédos mondat határán működik.  


**Syntax**

```xml
<mstts:silence  type="string"  value="string"/>
```

**Attribútumok**

| Attribútum | Leírás | Kötelező/nem kötelező |
|-----------|-------------|---------------------|
| `type` | Itt adható meg a csend helye: <ul><li>Sortávolság – a szöveg elején </li><li>Farok – a szöveg végén </li><li>Sentenceboundary – a szomszédos mondatok között </li></ul> | Kötelező |
| `Value` | Megadja a szünet időtartamát másodpercben vagy ezredmásodpercben, ez az érték kisebb, mint 5000ms. Példák érvényes értékekre, `2s` és `500ms` | Kötelező |

**Példa** Ebben a példában az `mtts:silence` 200 MS csendet adja hozzá két mondat között.
```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">  
<voice name="en-US-AriaNeural"> 
<mstts:silence  type="Sentenceboundary" value="200ms"/> 
If we’re home schooling, the best we can do is roll with what each day brings and try to have fun along the way. 
A good place to start is by trying out the slew of educational apps that are helping children stay happy and smash their schooling at the same time. 
</voice> 
</speak> 
```

## <a name="specify-paragraphs-and-sentences"></a>Bekezdések és mondatok meghatározása

`p` és `s` az elemek a bekezdések és a mondatok jelölésére használatosak. Ezen elemek hiányában a szöveg-beszéd szolgáltatás automatikusan meghatározza a SSML-dokumentum szerkezetét.

Az `p` elem tartalmazhat szöveget és a következő elemeket:,,,,,, `audio` `break` `phoneme` `prosody` `say-as` `sub` `mstts:express-as` és `s` .

Az `s` elem tartalmazhat szöveget és a következő elemeket:,,,,, `audio` `break` `phoneme` `prosody` `say-as` `mstts:express-as` és `sub` .

**Syntax**

```XML
<p></p>
<s></s>
```

**Példa**

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
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

## <a name="use-phonemes-to-improve-pronunciation"></a>A fonémák használata a kiejtés javítására

Az `ph` elem a fonetikus KIEJTÉS SSML-dokumentumokban való használata. Az `ph` elem csak szöveget és más elemeket tartalmazhat. Az emberi olvasásra alkalmas beszédet mindig tartalékként adja meg.

A fonetikus ábécék olyan telefonokból állnak, amelyek betűkből, számokból vagy karakterből állnak, esetenként kombinációban. Minden telefon egyedi hangfelismerést ír elő. Ez ellentétben áll a latin ábécével, ahol bármely betű több beszélt hangot is jelenthet. Vegye figyelembe a "c" betű különböző kiejtéseit a "candy" és a "megszüntetés" kifejezésben, vagy a "th" betű kombinációjának különböző kiejtéseit a "Thing" és a "The" kifejezésben.

> [!NOTE]
> A fonémák címke nem támogatott ebben az 5 hangban (et-EE-AnuNeural, GA-IE-OrlaNeural, lt-LT-OnaNeural, LV-LV-EveritaNeural és MT-MT-GarceNeural).

**Syntax**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**Attribútumok**

| Attribútum | Leírás | Kötelező/nem kötelező |
|-----------|-------------|---------------------|
| `alphabet` | Megadja az attribútumban szereplő karakterlánc kiejtésének szintetizálása során használandó fonetikus ábécét `ph` . Az ábécét megadó karakterláncot kisbetűs betűkkel kell megadni. Az alábbiakban megadhatja a lehetséges ábécéket.<ul><li>`ipa`&ndash; <a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">Nemzetközi fonetikus ábécé</a></li><li>`sapi`&ndash; [Beszédfelismerési szolgáltatás fonetikus ábécéje](speech-ssml-phonetic-sets.md)</li><li>`ups`&ndash; <a href="https://documentation.help/Microsoft-Speech-Platform-SDK-11/17509a49-cae7-41f5-b61d-07beaae872ea.htm" target="_blank">Univerzális telefon készlet</a></li></ul><br>Az ábécé csak a `phoneme` elemre vonatkozik. | Választható |
| `ph` | Olyan telefonszámot tartalmazó karakterlánc, amely a szó kiejtését határozza meg a `phoneme` elemben. Ha a megadott karakterlánc nem felismerhető telefonokat tartalmaz, a szöveg-beszéd (TTS) szolgáltatás elutasítja a teljes SSML-dokumentumot, és a dokumentumban megadott egyik beszédfelismerési kimenetet sem hozza létre. | Fonémák használata esetén kötelező. |

**Példák**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <phoneme alphabet="sapi" ph="iy eh n y uw eh s"> en-US </phoneme>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <s>His name is Mike <phoneme alphabet="ups" ph="JH AU"> Zhou </phoneme></s>
    </voice>
</speak>
```

## <a name="use-custom-lexicon-to-improve-pronunciation"></a>A kiejtés javítása egyéni lexikon használatával

Előfordulhat, hogy a szöveg-beszéd szolgáltatás nem tudja pontosan kiejteni a szót. Például egy vállalat neve vagy egy orvosi kifejezés. A fejlesztők meghatározhatják, hogy az egyes entitások hogyan legyenek beolvasva a SSML a `phoneme` és a `sub` címkék használatával. Ha azonban azt is meg kell határoznia, hogy az egyes entitások hogyan legyenek beolvasva, létrehozhat egy egyéni lexikont a `lexicon` címke használatával.

> [!NOTE]
> Az egyéni lexikon jelenleg támogatja az UTF-8 kódolást. 

> [!NOTE]
> Az egyéni lexikon nem támogatott ebben az 5 hangban (et-EE-AnuNeural, GA-IE-OrlaNeural, lt-LT-OnaNeural, LV-LV-EveritaNeural és MT-MT-GarceNeural).


**Syntax**

```XML
<lexicon uri="string"/>
```

**Attribútumok**

| Attribútum | Leírás                               | Kötelező/nem kötelező |
|-----------|-------------------------------------------|---------------------|
| `uri`     | A külső PLS-dokumentum címe. | Kötelező.           |

**Használat**

Ha meg szeretné határozni, hogy az egyes entitások hogyan legyenek beolvasva, létrehozhat egy egyéni lexikont, amely. XML vagy. pls fájlként van tárolva. A következő egy minta. xml fájl.

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

Az `lexicon` elem legalább egy elemet tartalmaz `lexeme` . Minden `lexeme` elem tartalmaz legalább egy `grapheme` elemet, és egy vagy több `grapheme` , `alias` és `phoneme` elemet. Az `grapheme` elem a <a href="https://www.w3.org/TR/pronunciation-lexicon/#term-Orthography" target="_blank">helyesírást </a>leíró szöveget tartalmaz. Az `alias` elemek a betűszó vagy egy rövidített kifejezés kiejtésének jelzésére szolgálnak. Az `phoneme` elem a kiejtését leíró szöveget tartalmaz `lexeme` .

Fontos megjegyezni, hogy az egyéni lexikon használatával nem állítható be közvetlenül egy kifejezés kiejtése. Ha a kiejtést egy betűszóhoz vagy egy rövidített kifejezéshez kell beállítania, először adjon meg egy `alias` , majd társítsa a- `phoneme` t `alias` . Például:

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

Azt is megteheti, hogy közvetlenül `alias` a betűszóhoz vagy rövidített kifejezéshez adja meg a vártat. Például:
```xml
  <lexeme>
    <grapheme>Scotland MV</grapheme> 
    <alias>Scotland Media Wave</alias> 
  </lexeme>
```

> [!IMPORTANT]
> Az `phoneme` elem nem tartalmazhat szóközöket az IPA használatakor.

További információ az egyéni lexikon-fájlról: a [kiejtési lexikon specifikációjának (pls) 1,0-es verziója](https://www.w3.org/TR/pronunciation-lexicon/).

Ezután tegye közzé az egyéni lexikon-fájlt. Habár nem rendelkezünk korlátozásokkal a fájl tárolásához, az [Azure Blob Storage](../../storage/blobs/storage-quickstart-blobs-portal.md)használatát javasoljuk.

Miután közzétette az egyéni lexikont, hivatkozhat rá a SSML.

> [!NOTE]
> Az `lexicon` elemnek a elemen belül kell lennie `voice` .

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" 
          xmlns:mstts="http://www.w3.org/2001/mstts" 
          xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <lexicon uri="http://www.example.com/customlexicon.xml"/>
        BTW, we will be there probably at 8:00 tomorrow morning.
        Could you help leave a message to Robert Benigni for me?
    </voice>
</speak>
```

Ha ezt az egyéni lexikont használja, a "BTW" a "by the way" néven lesz beolvasva. A "Benigni" a megadott IPA "bɛ tɛsɨfɒn ni ː nji" lesz beolvasva.  

**Korlátozások**
- Fájlméret: az egyéni lexikon fájlméretének maximális korlátja 100 kb, ha ez meghaladja a méretet, a szintézisi kérelem sikertelen lesz.
- Lexikon gyorsítótárának frissítése: az egyéni lexikont a rendszer az első betöltéskor kulcsként fogja gyorsítótárazni a TTS szolgáltatásban. Az azonos URI-val rendelkező lexikon 15 percen belül nem lesz újratöltve, ezért az egyéni lexikon-módosításnak 15 percnél hosszabb ideig kell megvárnia, hogy érvénybe lépjen.

**Beszédfelismerési szolgáltatás fonetikus készletei**

A fenti mintában a nemzetközi fonetikus ábécét használjuk, más néven IPA-telefont. Javasoljuk, hogy a fejlesztők az IPA-t használják, mivel ez a nemzetközi szabvány. Egyes IPA-karakterek esetében az "előre összeállított" és a "kibontott" verzió szerepel a Unicode-ban való Ábrázoláskor. Az egyéni lexikon csak a kibontott Unicode-ket támogatja.

Figyelembe véve, hogy az IPA nem könnyen megjegyezhető, a beszédfelismerési szolgáltatás hét nyelvhez (,,,,, `en-US` `fr-FR` `de-DE` `es-ES` `ja-JP` `zh-CN` és `zh-TW` ) is definiál egy fonetikus készletet.

A `sapi` (z) és a (z) az `alphabet` Egyéni lexikonokkal rendelkező attribútumhoz a (z) az alábbi ábrán látható módon használhatja:

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

További információ a beszédfelismerési szolgáltatás részletes fonetikus ábécével kapcsolatban: [beszédfelismerési szolgáltatás fonetikus készletei](speech-ssml-phonetic-sets.md).

## <a name="adjust-prosody"></a>Prosody módosítása

Ezzel a beállítással `prosody` adható meg a szöveg és a beszéd kimenetének változása a szurok, a kontúr, a tartomány, a sebesség, az időtartam és a kötet változásaihoz. Az `prosody` elem tartalmazhat szöveget és a következő elemeket:,,,,,, `audio` `break` `p` `phoneme` `prosody` `say-as` `sub` és `s` .

Mivel a prosodic-attribútumok értékei a széles skálán változhatnak, a beszédfelismerő felismeri a hozzárendelt értékeket arra a javaslatra, hogy a kiválasztott hang tényleges prosodic értékei legyenek. A szöveg-beszéd szolgáltatás korlátozza vagy helyettesíti a nem támogatott értékeket. Példa a nem támogatott értékekre: 1 MHz vagy 120-es kötet.

**Syntax**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**Attribútumok**

| Attribútum | Leírás | Kötelező/nem kötelező |
|-----------|-------------|---------------------|
| `pitch` | Megadja a szöveg alappályáját. A szurok a következőképpen fejezhető ki:<ul><li>Abszolút érték, amelyet a szám a "Hz" (Hertz) kifejezéssel jelez. Például: `<prosody pitch="600Hz">some text</prosody>`.</li><li>Egy relatív érték, amelyet a rendszer a "+" vagy "-" előtaggal, majd a "Hz" vagy "St" előtaggal jelöl, amely a szurok módosításához szükséges összeget határozza meg. Például: `<prosody pitch="+80Hz">some text</prosody>` vagy `<prosody pitch="-2st">some text</prosody>` . A "Szent" érték azt jelzi, hogy a változási egység semitone-e, amely a normál diatonikus-skálán lévő hang (fél lépés) fele.</li><li>Állandó érték:<ul><li>x – alacsony</li><li>alacsony</li><li>közepes</li><li>magas</li><li>x – magas</li><li>alapértelmezett</li></ul></li></ul> | Választható |
| `contour` |A kontúr mostantól támogatja a neurális és a standard hangokat is. A kontúr a szurok változásait jelöli. Ezek a változások a beszédfelismerési kimenetben megadott időpontokban a célok tömbje jelennek meg. Az egyes célkitűzéseket paraméter párok halmaza határozza meg. Például: <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>Az egyes paraméterekben az első érték határozza meg a szurok változásának helyét a szöveg időtartamának százalékában. A második érték határozza meg a szurok növelésének vagy csökkentésének mértékét, egy relatív érték vagy a szurok enumerálási értéke alapján (lásd: `pitch` ). | Választható |
| `range` | Egy érték, amely a szövegben lévő szurok tartományát jelöli. `range`A leíráshoz megadhatja az azonos abszolút értékeket, relatív értékeket vagy enumerálási értékeket `pitch` . | Választható |
| `rate` | Megadja a szöveg beszédi arányát. `rate`A következőképpen lehet kifejezni:<ul><li>Egy relatív érték, amely az alapértelmezett érték szorzóként funkcionál. Az *1* érték például nem változik a díjszabásban. Az *0,5* -es érték a sebesség felére csökkentéset eredményez. A *3* érték a ráta beutazását eredményezi.</li><li>Állandó érték:<ul><li>x – lassú</li><li>lassú</li><li>közepes</li><li>gyors</li><li>x – gyors</li><li>alapértelmezett</li></ul></li></ul> | Választható |
| `duration` | Az az időtartam, amelynek el kell telnie, amíg a Speech szintézis (TTS) szolgáltatás beolvassa a szöveget, másodpercben vagy ezredmásodpercben. Például: *2s* vagy *1800ms*. Az időtartam csak a standard hangokat támogatja.| Választható |
| `volume` | Megadja a beszélő hangjának hangerejét. A kötet a következőképpen fejezhető ki:<ul><li>Abszolút érték 0,0 és 100,0 közötti tartományban, a *legcsendestől* a *leghangosabbig*. Például 75. Az alapértelmezett érték a 100,0.</li><li>Egy relatív érték, amelyet a rendszer a "+" vagy "-" előtaggal jelöl meg, amely a kötet módosításának mértékét határozza meg. Például: + 10 vagy-5,5.</li><li>Állandó érték:<ul><li>csendes</li><li>x – Soft</li><li>puha</li><li>közepes</li><li>hangos</li><li>x – hangos</li><li>alapértelmezett</li></ul></li></ul> | Választható |

### <a name="change-speaking-rate"></a>Beszéd arányának módosítása

A felszólalási arány a Word vagy a mondat szintjén is alkalmazható neurális hangokra és standard hangokra. 

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

A kötetek módosításai a Word vagy a mondat szintjén is alkalmazhatók a standard hangokra. Míg a kötetek módosításai csak a mondat szintjén alkalmazhatók a neurális hangokra.

**Példa**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <prosody volume="+20.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-pitch"></a>Szurok módosítása

A szurok módosítása a Word vagy a mondat szintjén is alkalmazható a standard hangokra. Míg a szurok módosításait csak a mondat szintjén lehet a neurális hangokra alkalmazni.

**Példa**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Guy24kRUS">
        Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
    </voice>
</speak>
```

### <a name="change-pitch-contour"></a>Szurok körvonalának módosítása

> [!IMPORTANT]
> A Pitch Contour változásai mostantól a neurális hangokkal is támogatottak.

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
## <a name="say-as-element"></a>Say-as elem

`say-as` egy opcionális elem, amely megadja az elem szövegének típusát (például szám vagy dátum). Ez útmutatást nyújt a beszédfelismerési motornak a szöveg kiejtéséről.

**Syntax**

```XML
<say-as interpret-as="string" format="digit string" detail="string"> <say-as>
```

**Attribútumok**

| Attribútum | Leírás | Kötelező/nem kötelező |
|-----------|-------------|---------------------|
| `interpret-as` | Megadja az elem szövegének tartalomtípusát. A típusok listáját az alábbi táblázat tartalmazza. | Kötelező |
| `format` | További információkat nyújt az elem szövegének pontos formázásáról olyan tartalomtípusok esetében, amelyek kétértelmű formátummal rendelkezhetnek. A SSML az azokat használó tartalomtípusok formátumait határozzák meg (lásd az alábbi táblázatot). | Választható |
| `detail` | Megadja a megbeszélni kívánt részletességi szintet. Ez az attribútum például kérheti, hogy a Speech szintézis motor "központozás" jelölést küldjön. Nincs definiálva szabványos érték a következőhöz: `detail` . | Választható |

<!-- I don't understand the last sentence. Don't we know which one Cortana uses? -->

A `interpret-as` és attribútumok támogatott tartalomtípusai a következők: `format` `format`Csak akkor adja meg az attribútumot, ha a értéke `interpret-as` dátum és idő.

| értelmezés – as | formátumban | Értelmezés |
|--------------|--------|----------------|
| `address` | | A szöveget címként kell kimondani. A Speech szintézis motorja:<br /><br />`I'm at <say-as interpret-as="address">150th CT NE, Redmond, WA</say-as>`<br /><br />Mint "én vagyok a 150th Court North East Redmond Washington." |
| `cardinal`, `number` | | A szöveg kardinális számként van kimondva. A Speech szintézis motorja:<br /><br />`There are <say-as interpret-as="cardinal">3</say-as> alternatives`<br /><br />Ahogy a "három alternatíva van." |
| `characters`, `spell-out` | | A szöveg külön betűként van kiírva (helyesírás). A Speech szintézis motorja:<br /><br />`<say-as interpret-as="characters">test</say-as>`<br /><br />"T E S T"-ként. |
| `date` | DMY, MDY, YMD, énh, ym, My, MD, DM, d, m, y | A szöveget dátumként kell kimondani. Az `format` attribútum a dátum formátumát (*d = nap, m = hónap és y = év*) adja meg. A Speech szintézis motorja:<br /><br />`Today is <say-as interpret-as="date" format="mdy">10-19-2016</say-as>`<br /><br />Ahogy a mai év október tizenkilencedik 2016. |
| `digits`, `number_digit` | | A szöveget külön számjegyek sorozata beszéljük. A Speech szintézis motorja:<br /><br />`<say-as interpret-as="number_digit">123456789</say-as>`<br /><br />"1 2 3 4 5 6 7 8 9". |
| `fraction` | | A szöveg tört számként van kimondva. A Speech szintézis motorja:<br /><br /> `<say-as interpret-as="fraction">3/8</say-as> of an inch`<br /><br />"Három nyolcadik egy hüvelyk". |
| `ordinal` | | A szöveg sorszámként van kimondva. A Speech szintézis motorja:<br /><br />`Select the <say-as interpret-as="ordinal">3rd</say-as> option`<br /><br />"Válassza a harmadik lehetőséget". |
| `telephone` | | A szöveg telefonszámként van kimondva. Az `format` attribútum tartalmazhat országkódot jelölő számjegyeket. Például: "1" az Egyesült Államok vagy "39" esetében Olaszország esetében. A beszédfelismerési motor felhasználhatja ezeket az információkat, hogy a kiejtését egy telefonszámra irányítsa. A telefonszámon szerepelhet az országkód is, és ha igen, elsőbbséget élvez az országkód felett `format` . A Speech szintézis motorja:<br /><br />`The number is <say-as interpret-as="telephone" format="1">(888) 555-1212</say-as>`<br /><br />A "saját szám a 8 8 8 5 5 5 1 2 1 2-es körzetszám". |
| `time` | hms12, hms24 | A szöveget a rendszer időpontként beszéli. Az `format` attribútum azt adja meg, hogy az idő 12 órás (hms12) vagy 24 órás (hms24) óra használatával van-e megadva. Használjon kettőspontot az órákat, perceket és másodperceket jelölő számok elválasztásához. Az alábbi példák érvényesek: 12:35, 1:14:32, 08:15 és 02:50:45. A Speech szintézis motorja:<br /><br />`The train departs at <say-as interpret-as="time" format="hms12">4:00am</say-as>`<br /><br />Ahogy "a vonat négyen M." |

**Használat**

Az `say-as` elem csak szöveget tartalmazhat.

**Példa**

A Speech szintézis motor a következő példát szemlélteti: "az első kérés egy, a tizenkilencedik 20 10-es, a korai érkezési idő pedig a 12 35 ÓRAKOR."
 
```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <p>
        Your <say-as interpret-as="ordinal"> 1st </say-as> request was for <say-as interpret-as="cardinal"> 1 </say-as> room
        on <say-as interpret-as="date" format="mdy"> 10/19/2010 </say-as>, with early arrival at <say-as interpret-as="time" format="hms12"> 12:35pm </say-as>.
        </p>
    </voice>
</speak>
```

## <a name="add-recorded-audio"></a>Rögzített hang hozzáadása

`audio` egy opcionális elem, amely lehetővé teszi, hogy MP3-hangfelvételt helyezzen be egy SSML-dokumentumba. A hangelem törzse tartalmazhat egyszerű szöveges vagy SSML jelölést, amely akkor beszél, ha a hangfájl nem érhető el, vagy nem játszható le. Emellett az `audio` elem tartalmazhat szöveget és a következő elemeket:,,,,,, `audio` `break` `p` `s` `phoneme` `prosody` `say-as` és `sub` .

A SSML-dokumentumban szereplő összes hangnak meg kell felelnie a következő követelményeknek:

* Az MP3-t egy internetről elérhető HTTPS-végponton kell üzemeltetni. HTTPS szükséges, és az MP3-fájlt üzemeltető tartománynak érvényes, megbízható TLS/SSL-tanúsítványt kell tartalmaznia.
* Az MP3-fájlnak érvényes MP3-fájlként (MPEG v2) kell lennie.
* A bitsűrűségnek 48 kbps-nak kell lennie.
* A mintavételi sebességnek 16 000 Hz-nek kell lennie.
* Az egyetlen válaszban lévő összes szöveges és hangfájl együttes teljes ideje nem lehet nagyobb, mint 90 (90) másodperc.
* Az MP3 nem tartalmazhat ügyfél-specifikus vagy más bizalmas információt.

**Syntax**

```xml
<audio src="string"/></audio>
```

**Attribútumok**

| Attribútum | Leírás                                   | Kötelező/nem kötelező                                        |
|-----------|-----------------------------------------------|------------------------------------------------------------|
| `src`     | Megadja a hangfájl helyét/URL-címét. | Kötelező, ha a hangelemet használja a SSML-dokumentumban. |

**Példa**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
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

## <a name="add-background-audio"></a>Háttérbeli hang hozzáadása

Az `mstts:backgroundaudio` elem lehetővé teszi a háttérbeli hang hozzáadását a SSML-dokumentumokhoz (vagy szöveg-beszéd típusú hangfájlt keverve). A `mstts:backgroundaudio` háttérbeli hangfájlok a háttérben, a szöveg és a beszéd elején elhalványulnak, és a szöveg és a beszéd végén elhalványulnak.

Ha a megadott háttérbeli hang rövidebb, mint a szöveg-beszéd vagy a Halványítás, a rendszer hurokba kerül. Ha a szöveg-beszédnél hosszabb, akkor leáll, amikor befejeződött a Halványítás.

SSML-dokumentumok esetében csak egy háttér-hangfájl engedélyezett. `audio`Az elemen belüli címkéket azonban intersperse is `voice` felvehet, ha további hanganyagot szeretne hozzáadni a SSML-dokumentumhoz.

**Syntax**

```XML
<mstts:backgroundaudio src="string" volume="string" fadein="string" fadeout="string"/>
```

**Attribútumok**

| Attribútum | Leírás | Kötelező/nem kötelező |
|-----------|-------------|---------------------|
| `src` | Megadja a háttér hangfájljának helyét/URL-címét. | Kötelező, ha a SSML-dokumentumban háttér hang van használatban. |
| `volume` | Meghatározza a háttér-hangfájl kötetét. **Elfogadott értékek**: `0` a `100` bezárólag. Az alapértelmezett érték `1`. | Választható |
| `fadein` | Meghatározza a háttérbeli hang "elhalványulás" időtartamát ezredmásodpercben. Az alapértelmezett érték `0` :, amely a nem áttűnéssel egyenértékű. **Elfogadott értékek**: `0` a `10000` bezárólag.  | Választható |
| `fadeout` | Meghatározza, hogy a háttérbeli hang mennyi ideig elhalványul ezredmásodpercben. Az alapértelmezett érték a `0` , ami egyenértékű a kihalványítás nélkül. **Elfogadott értékek**: `0` a `10000` bezárólag.  | Választható |

**Példa**

```xml
<speak version="1.0" xml:lang="en-US" xmlns:mstts="http://www.w3.org/2001/mstts">
    <mstts:backgroundaudio src="https://contoso.com/sample.wav" volume="0.7" fadein="3000" fadeout="4000"/>
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)">
        The text provided in this document will be spoken over the background audio.
    </voice>
</speak>
```

## <a name="next-steps"></a>Következő lépések

* [Nyelvi támogatás: hangok, területi beállítások, nyelvek](language-support.md)
