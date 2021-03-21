---
title: Migrálás v3-Fordítóba
titleSuffix: Azure Cognitive Services
description: Ez a cikk a v2-ről az Azure Cognitive Services Translator-ra történő Migrálás megkönnyítését ismerteti.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: 13c4d39284fad293c945f8b7e31076dccee84fda
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98896833"
---
# <a name="translator-v2-to-v3-migration"></a>Translator v2 – v3 Migrálás

> [!NOTE]
> A v2 2018. április 30-ig elavult. Telepítse át az alkalmazásait a v3-ra, hogy kihasználhassa a kizárólag a v3 verzióban elérhető új funkciókat. A v2 2021. május 24-én megszűnik. 

A Microsoft Translator csapata a Translator 3 (v3) verzióját adta ki. Ez a kiadás új szolgáltatásokat, elavult metódusokat és új formátumot tartalmaz a Microsoft Translator Service-ből történő küldéshez és adatok fogadásához. Ebből a dokumentumból megtudhatja, hogyan módosíthatja az alkalmazások a v3-es verzió használatát. 

A dokumentum vége hasznos hivatkozásokat tartalmaz, amelyekről további információt talál.

## <a name="summary-of-features"></a>Funkciók összefoglalása

* A Azure Portal összes díjszabási szintjére nem vonatkozik nyomkövetés – a v3 No-Trace. Ez a funkció azt jelenti, hogy a V3 API-nak nem küldött szöveg a Microsoft által lesz mentve.
* A JSON-XML-t a JSON váltja fel. A szolgáltatásnak küldött és a szolgáltatástól kapott összes adatok JSON formátumúak.
* Több célnyelv egyetlen kérelemben – a fordítási módszer több "to" nyelvet is elfogad egyetlen kérelemben való fordításhoz. Egy kérés például lehet "from" angol és "–" német, spanyol, Japán vagy más nyelvi csoport.
* Kétnyelvű szótár – egy kétnyelvű szótári metódus lett hozzáadva az API-hoz. Ez a metódus a "lookup" és a "példák" kifejezéseket tartalmazza.
* Átbetűzés – átbetűzés metódus lett hozzáadva az API-hoz. Ez a metódus a szavakat és a mondatokat egy parancsfájlban konvertálja (például Arab) egy másik parancsfájlba (például Latin betűs).
* Nyelvek – az új "languages" metódus JSON formátumú nyelvi információkat biztosít a "Translate", a "Dictionary" és a "átbetűzés" metódusokkal való használathoz.
* Új fordítás – új képességek lettek hozzáadva a "Translate" metódushoz, amely a v2 API különböző funkcióit külön módszerként támogatja. Ilyen például a TranslateArray.
* A Speak metódus – a szöveg és a Beszédfelismerés funkció már nem támogatott a Microsoft Translatorben. A beszédfelismerési funkció a [Microsoft Speech Service](../speech-service/text-to-speech.md)-ben érhető el.

A v2 és v3 metódusok következő listája a v2-vel kapott funkciókat biztosító v3 metódusokat és API-kat azonosítja.

| V2 API-módszer   | V3 API-kompatibilitás |
|:----------- |:-------------|
| `Translate`     | [Fordítása](reference/v3-0-translate.md)          |
| `TranslateArray`      | [Fordítása](reference/v3-0-translate.md)        |
| `GetLanguageNames`      | [Nyelvek](reference/v3-0-languages.md)         |
| `GetLanguagesForTranslate`     | [Nyelvek](reference/v3-0-languages.md)       |
| `GetLanguagesForSpeak`      | [Microsoft Speech Service](../speech-service/language-support.md#text-to-speech)         |
| `Speak`     | [Microsoft Speech Service](../speech-service/text-to-speech.md)          |
| `Detect`     | [Kinyomoz](reference/v3-0-detect.md)         |
| `DetectArray`     | [Kinyomoz](reference/v3-0-detect.md)         |
| `AddTranslation`     | A szolgáltatás már nem támogatott       |
| `AddTranslationArray`    | A szolgáltatás már nem támogatott          |
| `BreakSentences`      | [BreakSentence](reference/v3-0-break-sentence.md)       |
| `GetTranslations`      | A szolgáltatás már nem támogatott         |
| `GetTranslationsArray`      | A szolgáltatás már nem támogatott         |

## <a name="move-to-json-format"></a>Áthelyezés JSON formátumba

A Microsoft Translator Translation v2 XML formátumban fogadta el és adta vissza az értékeket. A v3-as verzióban az API használatával küldött és fogadott összes adatfájl JSON formátumban van. Az XML-fájl már nem fogadható el, vagy nem ad vissza a v3-as verzióban.

Ez a módosítás a v2 Text Translation API-hoz írt alkalmazások számos aspektusát érinti. Példa: a languages API a szöveg fordítását, az írást és a két szótár módszereit adja vissza. Egy hívásban az összes metódushoz kérheti az összes nyelvi információt, vagy egyenként is megkérheti őket.

A languages metódus nem igényel hitelesítést; a következő hivatkozásra kattintva megtekintheti az összes nyelvi információt a (z) v3 verziójában a JSON-ban:

[https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation, szótár, angol nyelven](https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration)

## <a name="authentication-key"></a>Hitelesítési kulcs

A v2-hez használt hitelesítési kulcs a v3-as verzióhoz lesz elfogadva. Nem kell új előfizetést beszereznie. Az egész évben-áttelepítési időszak során a v2 és a v3 kombinációt is használhatja az alkalmazásaiban, így könnyebben szabadíthat fel új verziókat, miközben továbbra is a v2-XML-ről v3-JSON-ra végez áttelepítést.

## <a name="pricing-model"></a>Díjszabási modell

A Microsoft Translator v3 díjszabása megegyezik a v2 díjszabással. /karakter, a szóközöket is beleértve. A v3 új funkciói bizonyos változtatásokat hajtanak végre a számlázási számításokban.

| V3 módszer   | A számlázáshoz megszámolt karakterek |
|:----------- |:-------------|
| `Languages`     | Nincs elküldött karakter, nincs megszámolva, díjmentes.          |
| `Translate`     | A Count függvény azon alapszik, hogy hány karaktert küld a rendszer a fordításhoz, és hogy hány nyelvet fordítanak le a rendszer. 50 karakter elküldve, a kért 5 nyelv pedig 50x5.           |
| `Transliterate`     | Az írásra elküldött karakterek száma.         |
| `Dictionary lookup & example`     | A Szótárbeli kereséshez elküldött karakterek száma és a példák száma.         |
| `BreakSentence`     | Díjmentes.       |
| `Detect`     | Díjmentes.      |

## <a name="v3-end-points"></a>V3 végpontok

Globális

* api.cognitive.microsofttranslator.com

## <a name="v3-api-text-translations-methods"></a>V3 API Text translations metódusok

[`Languages`](reference/v3-0-languages.md)

[`Translate`](reference/v3-0-translate.md)

[`Transliterate`](reference/v3-0-transliterate.md)

[`BreakSentence`](reference/v3-0-break-sentence.md)

[`Detect`](reference/v3-0-detect.md)

[`Dictionary/lookup`](reference/v3-0-dictionary-lookup.md)

[`Dictionary/example`](reference/v3-0-dictionary-examples.md)

## <a name="compatibility-and-customization"></a>Kompatibilitás és testreszabás

> [!NOTE]
> 
> A Microsoft Translator hub 2019. május 17-én megszűnik. [Tekintse meg a fontos áttelepítési információkat és dátumokat](https://www.microsoft.com/translator/business/hub/).   

A Microsoft Translator v3 alapértelmezés szerint neurális gépi fordítást használ. Ezért nem használható a Microsoft Translator hub használatával. A Translator hub csak az örökölt statisztikai gépi fordítást támogatja. Az neurális fordítás testreszabása mostantól a Custom Translator használatával érhető el. [További információ a neurális gépi fordítás testreszabásáról](custom-translator/overview.md)

A v3 Text API-val való neurális fordítás nem támogatja a szabványos kategóriák használatát (SMT, Speech, Tech, generalnn).

| Verzió | Végpont | GDPR processzor megfelelősége | Translator hub használata | Egyéni fordító használata (előzetes verzió) |
| :------ | :------- | :------------------------ | :----------------- | :------------------------------ |
|Translator 2. verzió|    api.microsofttranslator.com|    Nem    |Igen    |Nem|
|Translator 3. verzió|    api.cognitive.microsofttranslator.com|    Igen|    Nem|    Igen|

**Translator 3. verzió**
* Általánosan elérhető és teljes mértékben támogatott.
* A GDPR megfelelő, és megfelel az ISO 20001-es és 20018-es, valamint a SOC 3 minősítési követelményeknek. 
* Lehetővé teszi az egyéni Translator (előzetes verzió) használatával testreszabott neurális hálózati fordítói rendszerek meghívását, az új Translator NMT testreszabási funkcióját. 
* A nem biztosít hozzáférést a Microsoft Translator hub használatával létrehozott egyéni fordítási rendszerekhez.

Ha a api.cognitive.microsofttranslator.com-végpontot használja, a fordító 3. verzióját használja.

**Translator 2. verzió**
* Nem felel meg az összes ISO-20001, 20018 és SOC 3 minősítési követelménynek. 
* A nem teszi lehetővé a fordítói testreszabási funkcióval testreszabott neurális hálózati fordítói rendszerek meghívását.
* Hozzáférést biztosít a Microsoft Translator hub használatával létrehozott egyéni fordítási rendszerekhez.
* Ha a api.microsofttranslator.com-végpontot használja, a fordító 2-es verzióját használja.

A fordító egyik verziója sem hoz létre egy rekordot a fordításokról. A fordítások soha nem oszthatók meg senkivel. További információ a [Translator No-Trace](https://www.aka.ms/NoTrace) weboldaláról.

## <a name="links"></a>Hivatkozások

* [A Microsoft adatvédelmi szabályzata](https://privacy.microsoft.com/privacystatement)
* [Jogi információk Microsoft Azure](https://azure.microsoft.com/support/legal)
* [Online Szolgáltatások Feltételei](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [A V 3.0 dokumentációjának megtekintése](reference/v3-0-reference.md)
