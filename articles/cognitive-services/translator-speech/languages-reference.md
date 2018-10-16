---
title: Translator Speech API nyelvek metódus
titleSuffix: Azure Cognitive Services
description: A Translator Speech API nyelvek módszert használja.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: conceptual
ms.date: 05/18/18
ms.author: v-jansko
ms.openlocfilehash: b7005811898df9132be6bc199e26f6c6dc358618
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2018
ms.locfileid: "49345203"
---
# <a name="translator-speech-api-languages"></a>Beszédfordító API: nyelv

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Fordítói beszéd folyamatosan bővíti a szolgáltatás által támogatott nyelvek listáját. Ez az API segítségével a jelenleg elérhető a Translator Speech szolgáltatással való használatra nyelvek készletét felderíteni.

Az API-t elérhető nyelvek beolvasása használatának bemutatásához Kódminták érhetők el a [a Microsoft Translator Github-webhelyről](https://github.com/MicrosoftTranslator).

## <a name="implementation-notes"></a>Megvalósításhoz fűzött megjegyzések

/Languages beolvasása 

A beszéd, hogy az átírt szöveg lefordítása, és a fordítás szintetizált lefényképezze nyelvek széles érhető el.

Ügyfél használ a `scope` lekérdezési paraméter meghatározásához, hogy mely csoportok nyelveken, van érdekelné.

* **Hang-szöveg:** lekérdezési paraméterrel `scope=speech` lefényképezze beszéd szöveggé számára elérhető nyelveket a készletét lekéréséhez.
* **Szövegfordítás:** lekérdezési paraméterrel `scope=text` beolvasni az átírt szöveg lefordítása számára elérhető nyelveket készletét.
* **Szöveg-hang transzformációs:** lekérdezési paraméterrel `scope=tts` nyelvek és beszédhangot szintetizálásához lefordított szöveget beszéddé vissza lehet lekérdezni.

Egy ügyfél egyszerre lekérheti több választási lehetőségek vesszővel tagolt listájának megadásával. Például: `scope=speech,text,tts`.

Minden kért beállítása sikeres válasz tulajdonsággal rendelkező JSON-objektum.

```
{
    "speech": {
        //... Set of languages supported for speech-to-text
    },
    "text": {
        //... Set of languages supported for text translation
    },
    "tts": {
        //... Set of languages supported for text-to-speech
    }
}
```

Mivel az ügyfél használhatja a `scope` vissza kell adni a lekérdezési paraméter támogatott nyelveket, mely csoportok kiválasztásához, a tényleges választ előfordulhat, hogy csak egy részével fent látható az összes tulajdonság.

Az egyes tulajdonság megadott értéke a következő.

### <a name="speech-to-text-speech"></a>Hang-szöveg transzformációs (beszédfelismerés)

A hang-szöveg transzformációs tulajdonság társított értéket `speech`, egy szótár (kulcs, érték) párok. Minden egyes kulcs azonosítja a hang-szöveg transzformációs támogatja. A kulcs az azonosítója, amely az API-t, hogy az ügyfél továbbítja. A kulcshoz tartozó érték a következő tulajdonságokkal rendelkező objektum:

* `name`: A nyelv megjelenített neve.
* `language`: Annak a társított nyelven írt nyelvi címkéje. Olvassa el a "Szöveg traznakció".
A következő egy példa:

```
{
    "speech": {
        "en-US": { name: "English", language: "en" }
    }
}
```

### <a name="text-translation-text"></a>Szövegfordítás (szöveg)

Az értéket a `text` tulajdonság akkor is egy szótár, ahol minden egyes kulcs azonosítja az szövegfordítás támogatott nyelvet. A kulcshoz tartozó érték a nyelv ismerteti:

* `name`: A nyelv megjelenített neve.
* `dir`: Azaz írásmód `rtl` jobbról balra író nyelvek vagy `ltr` jobbról balra író nyelvek.

A következő egy példa:

```
{
    "text": {
        "en": { name: "English", dir: "ltr" }
    }
}
```

### <a name="text-to-speech-tts"></a>Szövegfelolvasás

A szöveg-hang transzformációs tulajdonság, szöveg-beszéd átalakítás, társított érték is egy szótár, ahol minden egyes kulcs azonosítja az egy támogatott hangalapú. Egy hang-objektum attribútumai a következők:

* `displayName`: A hang megjelenített neve.
* `gender`: A voice (Férfi vagy női) neme.
* `locale`: A hang és az elsődleges nyelv alkód régió alkód nyelvi címkéjét.
* `language`: Annak a társított nyelven írt nyelvi címkéje.
* `languageName`: A nyelv megjelenített neve.
* `regionName`: Megjelenített neve a régióban ehhez a nyelvhez.

A következő egy példa:

```
{
    "tts": {
        "en-US-Zira": {
            "displayName": "Zira",
            "gender": "female",
            "locale": "en-US",
            "languageName": "English",
            "regionName": "United States",
            "language": "en"
        }
}
```

### <a name="localization"></a>Honosítás
A szolgáltatás minden nyelven az "Accept-nyelv" fejléc szövegfordítás támogatott összes nyelv adja vissza.

### <a name="response-class-status-200"></a>Válasz osztály (állapota 200)
A támogatott nyelvek készletét leíró objektum.

ModelExample érték: 

Langagues {beszéd (object, nem kötelező), szöveg (object, nem kötelező), szöveg-beszéd átalakítás (object, nem kötelező)}

### <a name="headers"></a>Fejlécek

|Fejléc|Leírás|Típus|
:--|:--|:--|
X-RequestId:|Érték a kiszolgáló azonosításához a kérelem által generált, és használják hibaelhárítási célból.|sztring|

### <a name="parameters"></a>Paraméterek

|Paraméter|Leírás|Paraméter típusa|Adattípus|
|:--|:--|:--|:--|
|API-verzió    |Az ügyfél által kért API-verzió. Engedélyezett értékek a következők: `1.0`.|lekérdezés|sztring|
|scope  |Támogatott nyelvek, vagy térjen vissza az ügyfél beszédhangot részhalmazához. Ez a paraméter van megadva kulcsszavak vesszővel elválasztott listáját. A következő kulcsszavak érhetők el:<ul><li>`speech`: A speech lefényképezze a támogatott nyelvek készletét nyújtja.</li><li>`tts`: Az a szöveg-beszéd átalakítás támogatott beszédhangot készletét nyújtja.</li><li>`text`: Az a szöveg fordítása támogatott nyelvek készletét nyújtja.</li></ul>Ha az érték nincs megadva, az értékét `scope` alapértelmezés szerint a `text`.|lekérdezés|sztring|
|X-ClientTraceId    |Egy ügyfél által létrehozott GUID egy kérelmet nyomon követéséhez használható. Megkönnyítése érdekében kapcsolatos hibák elhárítása, ügyfelek kell minden egyes kérelemmel adjon meg új értéket, és azt.|header|sztring|
|Fogadja el nyelv    |A mezők a válaszban némelyike régiók és nyelvek neve. Ez a paraméter használatával határozza meg a nyelvet, amelyben a nevét adja vissza. A nyelv azáltal, hogy megfelelően formázott BCP-47 nyelvi címke van megadva. Válassza ki a címke nyelv azonosítók hibát adott vissza a listából a `text` hatókör. Nem támogatott nyelvek a nevek angol nyelven szerepelnek.<br/>Például használja az értéket `fr` kérése nevek francia nyelvű, vagy használja az értéket `zh-Hant` hagyományos kínai kérelem nevek.|header|sztring|
    
### <a name="response-messages"></a>Parancsválasz-üzeneteket

|HTTP-állapotkód|Ok|
|:--|:--|
|400|Hibás kérés. Ellenőrizze, hogy biztosítsa azok érvényes bemeneti paramétereket. A válasz objektum tartalmazza a hiba részletes leírását.|
|429|Túl sok kérelmet.|
|500|Hiba történt. Ha a hiba továbbra is fennáll, jelentse az ügyfél nyomkövetési azonosító (X-ClientTraceId), vagy kérelemazonosító (X-kérelemazonosító).|
|503|A kiszolgáló átmenetileg nem érhető el. Ismételje meg a kérelmet. Ha a hiba továbbra is fennáll, jelentse az ügyfél nyomkövetési azonosító (X-ClientTraceId), vagy kérelemazonosító (X-kérelemazonosító).|
