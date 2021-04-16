---
title: Translator Translate metódus
titleSuffix: Azure Cognitive Services
description: A Translator Translate metódusának szövegfordító paraméterei, fejlécei és Azure Cognitive Services üzenetei.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 08/06/2020
ms.author: lajanuar
ms.openlocfilehash: 7c92fb7f5de5542af08d1335bea745557a330f0d
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107506006"
---
# <a name="translator-30-translate"></a>Translator 3.0: Translate

Szöveget fordít le.

## <a name="request-url"></a>URL-cím kérése

Kérés küldése a `POST` következőre:

```HTTP
https://api.cognitive.microsofttranslator.com/translate?api-version=3.0
```

## <a name="request-parameters"></a>Kérelemparaméterek

A lekérdezési sztringen átadott kérelemparaméterek a következőek:

### <a name="required-parameters"></a>Kötelező paraméterek

<table width="100%">
  <th width="20%">Lekérdezési paraméter</th>
  <th>Leírás</th>
  <tr>
    <td>api-verzió</td>
    <td><em>Kötelező paraméter:</em>.<br/>Az ügyfél által kért API verziója. Az értéknek értékűnek kell <code>3.0</code> lennie.</td>
  </tr>
  <tr>
    <td>felhasználóként a(z)</td>
    <td><em>Kötelező paraméter:</em>.<br/>A kimeneti szöveg nyelvét adja meg. A célnyelvnek a <a href="./v3-0-languages.md"></a> hatókörben szereplő támogatott nyelvek egyikének kell <code>translation</code> lennie. A használatával például <code>to=de</code> németre fordíthat.<br/>Egyszerre több nyelvre is fordíthat, ha a lekérdezési sztringben megismétli a paramétert. A használatával például <code>to=de&to=it</code> németre és olaszra fordíthat.</td>
  </tr>
</table>

### <a name="optional-parameters"></a>Választható paraméterek

<table width="100%">
  <th width="20%">Lekérdezési paraméter</th>
  <th>Leírás</th>
  <tr>
    <td> értékről</td>
    <td><em>Nem kötelező paraméter.</em><br/>A bemeneti szöveg nyelvét adja meg. A támogatott nyelvek hatókörrel való keresésének segítségével megkeresi, hogy mely nyelvekről <a href="./v3-0-languages.md">fordíthat</a> <code>translation</code> le. Ha a paraméter nincs megadva, a rendszer automatikus nyelvfelismerést alkalmaz <code>from</code> a forrásnyelv meghatározásához. <br/><br/>A dinamikus szótár funkció használatához nem automatikus, hanem paramétert <code>from</code> <a href="/azure/cognitive-services/translator/dynamic-dictionary">kell</a> használnia.</td>
  </tr>  
  <tr>
    <td>textType (szövegtípus)</td>
    <td><em>Nem kötelező paraméter.</em><br/>Meghatározza, hogy a lefordított szöveg egyszerű vagy HTML-szöveg-e. Minden HTML-nek megfelelően formázott, teljes elemnek kell lennie. Lehetséges értékek: <code>plain</code> (alapértelmezett) vagy <code>html</code>.</td>
  </tr>
  <tr>
    <td>category</td>
    <td><em>Nem kötelező paraméter.</em><br/>Egy sztring, amely a fordítás kategóriáját (tartományát) határozza meg. Ezzel a paraméterrel lekért fordításokat a használatával készült <a href="../customization.md">Custom Translator.</a> Adja hozzá a kategóriaazonosítót a Custom Translator <a href="/azure/cognitive-services/translator/custom-translator/how-to-create-project#view-project-details">projekt részleteiből</a> ehhez a paraméterhez az üzembe helyezett testreszabott rendszer használata érdekében. Az alapértelmezett érték a következő: <code>general</code> .</td>
  </tr>
  <tr>
    <td>profanityAction</td>
    <td><em>Nem kötelező paraméter.</em><br/>Megadja, hogyan kell kezelni a profanitásokat a fordításokban. Lehetséges értékek: <code>NoAction</code> (alapértelmezett) <code>Marked</code> vagy <code>Deleted</code> . A trágárság kezelésének módjaiért lásd: <a href="#handle-profanity">Profanitáskezelés.</a></td>
  </tr>
  <tr>
    <td>profanityMarker</td>
    <td><em>Nem kötelező paraméter.</em><br/>Megadja, hogyan kell megjelölni a profanitásokat a fordításokban. Lehetséges értékek: <code>Asterisk</code> (alapértelmezett) vagy <code>Tag</code> . A trágárság kezelésének módjaiért lásd: <a href="#handle-profanity">Profanitáskezelés.</a></td>
  </tr>
  <tr>
    <td>includeAlignment</td>
    <td><em>Nem kötelező paraméter.</em><br/>Megadja, hogy a forrásszövegből a lefordított szövegbe illesztés legyen-e bevetve. Lehetséges értékek: <code>true</code> vagy <code>false</code> (alapértelmezett). </td>
  </tr>
  <tr>
    <td>includeSentenceLength (includeSentenceLength)</td>
    <td><em>Nem kötelező paraméter.</em><br/>Meghatározza, hogy a bemeneti szöveg és a lefordított szöveg mondathatárait is tartalmazza-e. Lehetséges értékek: <code>true</code> vagy <code>false</code> (alapértelmezett).</td>
  </tr>
  <tr>
    <td>suggestedFrom (javasolttól)</td>
    <td><em>Nem kötelező paraméter.</em><br/>Egy tartalék nyelvet ad meg, ha a bemeneti szöveg nyelve nem azonosítható. A nyelv automatikus észlelése a paraméter <code>from</code> kihagyása esetén lesz alkalmazva. Ha az észlelés sikertelen, <code>suggestedFrom</code> a rendszer feltételezi a nyelvet.</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td><em>Nem kötelező paraméter.</em><br/>Megadja a bemeneti szöveg parancsfájlját.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td><em>Nem kötelező paraméter.</em><br/>Megadja a lefordított szöveg parancsfájlját.</td>
  </tr>
  <tr>
    <td>allowFallback</td>
    <td><em>Nem kötelező paraméter.</em><br/>Megadja, hogy a szolgáltatás visszavethet-e egy általános rendszert, ha nem létezik egyéni rendszer. Lehetséges értékek: <code>true</code> (alapértelmezett) vagy <code>false</code> .<br/><br/><code>allowFallback=false</code> A azt adja meg, hogy a fordítás csak a kérés által megadottak szerint <code>category</code> betanított rendszereket használja. Ha az X nyelv Y nyelvre való fordításához az E nyelvvel való összefűzás szükséges, akkor a láncban (X->E és E->Y) minden rendszernek egyéninek kell lennie, és azonos kategóriába kell sorolni. Ha nem található rendszer az adott kategóriával, a kérés 400-as állapotkódot ad vissza. <code>allowFallback=true</code> A azt adja meg, hogy a szolgáltatás visszavehet egy általános rendszert, ha nem létezik egyéni rendszer.
</td>
  </tr>
</table> 

A kérelemfejlécek a következők:

<table width="100%">
  <th width="20%">Fejlécek</th>
  <th>Leírás</th>
  <tr>
    <td>Hitelesítési fejléc(ék)</td>
    <td><em>Szükséges kérelemfejléc.</em><br/>Lásd <a href="/azure/cognitive-services/translator/reference/v3-0-reference#authentication">az elérhető hitelesítési lehetőségeket.</a></td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td><em>Szükséges kérelemfejléc.</em><br/>Megadja a hasznos adat tartalomtípusát.<br/> Az elfogadott érték <code>application/json; charset=UTF-8</code> .</td>
  </tr>
  <tr>
    <td>Tartalom hossza</td>
    <td><em>Szükséges kérelemfejléc.</em><br/>A kérelem törzsének hossza.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td><em>Nem kötelező.</em><br/>Egy ügyfél által létrehozott GUID, amely egyedileg azonosítja a kérést. Kihagyhatja ezt a fejlécet, ha a nyomkövetési azonosítót a lekérdezési sztringbe foglalja egy nevű lekérdezési <code>ClientTraceId</code> paraméterrel.</td>
  </tr>
</table> 

## <a name="request-body"></a>A kérés törzse

A kérelem törzse egy JSON-tömb. Minden tömbelem egy JSON-objektum, amelynek a nevű sztringtulajdonság a `Text` lefordítandó sztringet jelöli.

```json
[
    {"Text":"I would really like to drive your car around the block a few times."}
]
```

Az alábbi korlátozások érvényesek:

* A tömbnek legalább 100 eleme lehet.
* A kérelemben szereplő teljes szöveg nem lehet hosszabb 10 000 karakternél, beleértve a szóközöket is.

## <a name="response-body"></a>Választörzs

A sikeres válasz egy JSON-tömb, amely a bemeneti tömb minden sztringjéhez egy-egy eredményt ad. Az eredményobjektum a következő tulajdonságokat tartalmazza:

  * `detectedLanguage`: Az észlelt nyelvet az alábbi tulajdonságokkal leíró objektum:

      * `language`: Az észlelt nyelv kódját képviselő sztring.

      * `score`: Lebegőérték, amely az eredmény megbízhatóságát jelzi. A pontszám nulla és 1 között van, az alacsony pontszám pedig alacsony megbízhatóságot jelez.

    A `detectedLanguage` tulajdonság csak akkor található meg az eredményobjektumban, ha a rendszer automatikus nyelvfelismerést kér.

  * `translations`: A fordítási eredmények tömbje. A tömb mérete megegyezik a lekérdezési paraméterben megadott `to` célnyelvek számával. A tömb minden eleme a következőket tartalmazza:

    * `to`: A célnyelv nyelvi kódját képviselő sztring.

    * `text`: Egy sztring, amely a lefordított szöveget adja meg.

    * `transliteration`: Egy objektum, amely a paraméter által megadott szkript lefordított szövegét `toScript` adja meg.

      * `script`: A célszk szkriptet megíró sztring.   

      * `text`: Egy sztring, amely a lefordított szöveget adja meg a cél szkriptben.

    Az `transliteration` objektum nem lesz beleértve, ha az átlitálás nem történik meg.

    * `alignment`: Egy nevű egyetlen sztringtulajdonságú objektum, amely a bemeneti szöveget `proj` lefordított szövegre leképezi. Az igazítási információ csak akkor van megadva, ha a kérelem `includeAlignment` paramétere `true` . Az igazítás a következő formátum sztringértékeként lesz visszaadva: `[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]]` .  A kettőspont elválasztja a kezdő és záró indexet, a kötőjel választja el a nyelveket, a szóköz pedig elválasztja a szavakat. Az egyik szó nulla, egy vagy több szóhoz igazodhat a másik nyelven, az igazított szavak pedig nem összefüggőek lehetnek. Ha nem áll rendelkezésre igazítási információ, az igazítási elem üres lesz. Példa [és korlátozások: Igazítási](#obtain-alignment-information) információk beszerzése.

    * `sentLen`: Egy olyan objektum, amely mondathatárokat ad vissza a bemeneti és kimeneti szövegekben.

      * `srcSentLen`: Egész szám, amely a bemeneti szövegben megadott mondatok hosszát adja meg. A tömb hossza a mondatok száma, az értékek pedig az egyes mondatok hossza.

      * `transSentLen`: Egész szám, amely a lefordított szövegben a mondatok hosszát jelenti. A tömb hossza a mondatok száma, az értékek pedig az egyes mondatok hossza.

    A mondathatárok csak akkor érvénybe, ha a kérés `includeSentenceLength` paramétere `true` .

  * `sourceText`: Egy nevű egyetlen sztringtulajdonságú objektum, amely a forrásnyelv alapértelmezett szkriptében adja meg a `text` bemeneti szöveget. `sourceText` A tulajdonság csak akkor van jelen, ha a bemenet egy olyan szkriptben van kifejezve, amely nem a szokásos szkript a nyelvhez. Ha például a bemenet arab, latin betűs írású, akkor ez ugyanaz az arab szöveg lesz, amely arab `sourceText.text` parancsfájlná lesz konvertálva.

A JSON-válaszokra a példák szakaszban [talál](#examples) példát.

## <a name="response-headers"></a>Válaszfejlécek

<table width="100%">
  <th width="20%">Fejlécek</th>
  <th>Leírás</th>
    <tr>
    <td>X-RequestId</td>
    <td>A szolgáltatás által a kérés azonosítására létrehozott érték. Hibaelhárítási célokra szolgál.</td>
  </tr>
  <tr>
    <td>X-MT-System</td>
    <td>Megadja a fordításhoz kért "to" nyelvek fordításhoz használt rendszertípusát. Az érték sztringek vesszővel elválasztott listája. Minden sztring típust jelez:<br/><ul><li>Egyéni – A kérés tartalmaz egy egyéni rendszert, és legalább egy egyéni rendszert használt a fordítás során.</li><li>Csapat – Minden más kérés</li></td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Válasz állapotkódok

A kérések által visszaadott lehetséges HTTP-állapotkódok a következők. 

<table width="100%">
  <th width="20%">Állapotkód</th>
  <th>Leírás</th>
  <tr>
    <td>200</td>
    <td>Sikeres művelet.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>A lekérdezési paraméterek egyike hiányzik vagy érvénytelen. Az újrapróbálkozás előtt javítsa ki a kérelemparamétereket.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>A kérést nem lehetett hitelesíteni. Ellenőrizze, hogy a hitelesítő adatok meg vannak-e adva és érvényesek-e.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>A kérelem nincs engedélyezve. Ellenőrizze a részleteket tartalmazó hibaüzenetet. Ez gyakran azt jelzi, hogy a próba-előfizetéshez biztosított összes ingyenes fordítást felhasználták.</td>
  </tr>
  <tr>
    <td>408</td>
    <td>A kérést nem lehetett teljesíteni, mert hiányzik egy erőforrás. Ellenőrizze a részleteket tartalmazó hibaüzenetet. Egyéni használata esetén ez gyakran azt jelzi, hogy az egyéni fordítási rendszer még nem érhető el a <code>category</code> kérések kiszolgálására. A kérést egy várakozási idő (például 1 perc) után kell újra eltennünk.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>A kiszolgáló elutasította a kérelmet, mert az ügyfél túllépte a kérelmek korlátait.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Váratlan hiba történt. Ha a hiba továbbra is fennáll, jelentse a következővel: a hiba dátuma és időpontja, kérésazonosító a válaszfejlécből, és <code>X-RequestId</code> ügyfél-azonosító a kérelemfejlécből. <code>X-ClientTraceId</code></td>
  </tr>
  <tr>
    <td>503</td>
    <td>A kiszolgáló átmenetileg nem érhető el. Próbálja újra a kérést. Ha a hiba továbbra is fennáll, jelentse a következővel: a hiba dátuma és időpontja, kérésazonosító a válaszfejlécből, és <code>X-RequestId</code> ügyfél-azonosító a kérelemfejlécből. <code>X-ClientTraceId</code></td>
  </tr>
</table> 

Hiba esetén a kérés JSON-hibaüzenetet is visszaad. A hibakód egy 6 számjegyből álló szám, amely a háromjegyű HTTP-állapotkódot, majd egy 3 számjegyből álló számot kombinál a hiba további kategorizálásához. A gyakori hibakódok a [v3 Translator referenciaoldalán találhatók.](./v3-0-reference.md#errors) 

## <a name="examples"></a>Példák

### <a name="translate-a-single-input"></a>Egyetlen bemenet fordítása

Ez a példa bemutatja, hogyan fordíthat le egyetlen mondatot angolról egyszerűsített kínaira.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

A válasz törzse a következő:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    }
]
```

A tömb egy elemet tartalmaz, amely a bemenetben található egyetlen `translations` szöveg fordítását biztosítja.

### <a name="translate-a-single-input-with-language-auto-detection"></a>Egyetlen bemenet fordítása automatikus nyelvfelismeréssel

Ez a példa bemutatja, hogyan fordíthat le egyetlen mondatot angolról egyszerűsített kínaira. A kérelem nem adja meg a bemeneti nyelvet. Ehelyett a forrásnyelv automatikus észlelését használjuk.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

A válasz törzse a következő:

```
[
    {
        "detectedLanguage": {"language": "en", "score": 1.0},
        "translations":[
            {"text": "你好, 你叫什么名字？", "to": "zh-Hans"}
        ]
    }
]
```
A válasz hasonló az előző példában adott válaszhoz. Mivel a rendszer automatikus nyelvfelismerést kért, a válasz a bemeneti szöveghez észlelt nyelvre vonatkozó információkat is tartalmaz. A nyelv automatikus észlelése jobban működik a hosszabb bemeneti szövegekkel.

### <a name="translate-with-transliteration"></a>Fordítás átiratálással

Bővítjük ki az előző példát átliterálás hozzáadásával. A következő kérés egy latin betűvel írt kínai fordítást kér.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans&toScript=Latn" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

A válasz törzse a következő:

```
[
    {
        "detectedLanguage":{"language":"en","score":1.0},
        "translations":[
            {
                "text":"你好, 你叫什么名字？",
                "transliteration":{"script":"Latn", "text":"nǐ hǎo , nǐ jiào shén me míng zì ？"},
                "to":"zh-Hans"
            }
        ]
    }
]
```

A fordítás eredménye most már tartalmaz egy tulajdonságot, amely a lefordított szöveget latin `transliteration` karakterek használatával adja meg.

### <a name="translate-multiple-pieces-of-text"></a>Több szöveg lefordítása

Több sztring egyszerre történő lefordítása csupán egy sztringtömböt kell megadni a kérelem törzsében.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}, {'Text':'I am fine, thank you.'}]"
```

A válasz tartalmazza az összes szöveg lefordított szövegét pontosan ugyanabban a sorrendben, mint a kérésben.
A válasz törzse a következő:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    },            
    {
        "translations":[
            {"text":"我很好，谢谢你。","to":"zh-Hans"}
        ]
    }
]
```

### <a name="translate-to-multiple-languages"></a>Fordítás több nyelvre

Ez a példa bemutatja, hogyan fordíthatja le ugyanazt a bemenetet több nyelvre egyetlen kérésben.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

A válasz törzse a következő:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"},
            {"text":"Hallo, was ist dein Name?","to":"de"}
        ]
    }
]
```

### <a name="handle-profanity"></a>Profanitások kezelésével

A Translator szolgáltatás általában megőrzi a forrásban a fordításban jelen van trágárságokat. A trágárság foka és a trágár szavakat a különböző kulturális környezetek közötti különbségeknek megfelelően fel lehet erősni vagy csökkenteni a célnyelv trágársága miatt.

Ha el szeretné kerülni, hogy trágár szóhasználatot kap a fordításban, függetlenül attól, hogy a forrásszövegben trágár szó van-e, használhatja a profanitásszűrés lehetőséget. A beállítással eldöntheti, hogy szeretné-e törölni a trágárságokat, vagy a megfelelő címkékkel szeretné megjelölni a profanitásokat (így hozzáadhatja saját utófeldolgozási funkcióját), vagy nem szeretne semmilyen műveletet sem. Az elfogadott `ProfanityAction` értékei: `Deleted` , `Marked` és `NoAction` (alapértelmezett).

<table width="100%">
  <th width="20%">ProfanityAction</th>
  <th>Művelet</th>
  <tr>
    <td><code>NoAction</code></td>
    <td>Ez az alapértelmezett viselkedés. A trágárság a forrástól a célig fog haladni.<br/><br/>
    <strong>Példaforrás (japán):</strong>彼は következőです:<br/>
    <strong>Példafordítás (angol):</strong>Egy barom.
    </td>
  </tr>
  <tr>
    <td><code>Deleted</code></td>
    <td>A trágár szavakat a rendszer csere nélkül eltávolítja a kimenetből.<br/><br/>
    <strong>Példaforrás (japán):</strong>彼は következőです:<br/>
    <strong>Példafordítás (angol):</strong>ő egy.
    </td>
  </tr>
  <tr>
    <td><code>Marked</code></td>
    <td>A trágár szavakat egy jelölő váltja fel a kimenetben. A jelölő a paramétertől <code>ProfanityMarker</code> függ.<br/><br/>
A <code>ProfanityMarker=Asterisk</code> alatt a trágár szavak a következőre vannak <code>***</code> lecserélve:<br/>
    <strong>Példaforrás (japán):</strong>彼は következőです:<br/>
    <strong>Példafordítás (angol):</strong>Ő egy \* \* \* .<br/><br/>
A <code>ProfanityMarker=Tag</code> alatt trágár szavakat az XML-címkék &lt; profanitása és &gt; &lt; /profanitás vesz &gt; körül:<br/>
    <strong>Példaforrás (japán):</strong>彼は következőです:<br/>
    <strong>Példafordítás (angol):</strong>ő egy &lt; trágár &gt; jackass &lt; /profanity &gt; .
  </tr>
</table> 

Például:

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'This is a freaking good idea.'}]"
```
Ez a következőt adja vissza:

```
[
    {
        "translations":[
            {"text":"Das ist eine *** gute Idee.","to":"de"}
        ]
    }
]
```

Összehasonlítás a következővel:

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked&profanityMarker=Tag" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'This is a freaking good idea.'}]"
```

Az utolsó kérés a következőt adja vissza:

```
[
    {
        "translations":[
            {"text":"Das ist eine <profanity>verdammt</profanity> gute Idee.","to":"de"}
        ]
    }
]
```

### <a name="translate-content-with-markup-and-decide-whats-translated"></a>Tartalom fordítása jelölővel, és a lefordított tartalom eldöntés

Gyakran fordítják le az olyan tartalmakat, mint például egy HTML-oldal tartalma vagy egy XML-dokumentum tartalma. A tartalom `textType=html` címkékkel való lefordításakor lekérdezési paramétert is be kell foglalni. Emellett néha hasznos lehet kizárni bizonyos tartalmakat a fordításból. A attribútummal megadhatja `class=notranslate` azokat a tartalmakat, amelyek az eredeti nyelven maradnak. A következő példában az első elemen belüli tartalom nem lesz lefordítva, a második elem tartalma `div` `div` pedig le lesz fordítva.

```
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

Itt látható egy szemléltető mintakérés.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&textType=html" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'<div class=\"notranslate\">This will not be translated.</div><div>This will be translated.</div>'}]"
```

A válasz a következő:

```
[
    {
        "translations":[
            {"text":"<div class=\"notranslate\">This will not be translated.</div><div>这将被翻译。</div>","to":"zh-Hans"}
        ]
    }
]
```

### <a name="obtain-alignment-information"></a>Igazítási információk beszerzése

Az igazítás az alábbi formátum sztringértékeként lesz visszaadva a forrás minden szavára. Az egyes szavak információit szóköz választja el egymástól, beleértve a nem szóközt elválasztott nyelveket (parancsfájlokat), például a kínait:

[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]] *

Példa igazítási sztring: "0:0-7:10 1:2-11:20 3:4-0:3 3:4-4:6 5:5-21:21".

Más szóval a kettőspont választja el a kezdő és záró indexet, a kötőjel választja el a nyelveket, a szóköz pedig elválasztja a szavakat. Az egyik szó nulla, egy vagy több szóhoz igazodhat a másik nyelven, az igazított szavak pedig nem összefüggőek lehetnek. Ha nem áll rendelkezésre igazítási információ, az Igazítás elem üres lesz. Ebben az esetben a metódus nem ad vissza hibát.

Az igazítási információk fogadáshoz adja meg `includeAlignment=true` a értéket a lekérdezési sztringen.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeAlignment=true&quot; -H &quot;Ocp-Apim-Subscription-Key: <client-secret>&quot; -H &quot;Content-Type: application/json; charset=UTF-8&quot; -d &quot;[{'Text':'The answer lies in machine translation.'}]"
```

A válasz a következő:

```
[
    {
        "translations":[
            {
                "text":"La réponse se trouve dans la traduction automatique.",
                "to":"fr",
                "alignment":{"proj":"0:2-0:1 4:9-3:9 11:14-11:19 16:17-21:24 19:25-40:50 27:37-29:38 38:38-51:51"}
            }
        ]
    }
]
```

Az igazítási információ a karakterrel kezdődik, ami azt jelenti, hogy a forrásszöveg ( ) első három karaktere a lefordított szöveg első két karakterére `0:2-0:1` `The` ( ) van `La` leképezve.

#### <a name="limitations"></a>Korlátozások
Az igazítási információk beszerzése egy kísérleti funkció, amely lehetővé teszi a lehetséges kifejezésleképezésekkel kapcsolatos kutatások és tapasztalatok prototípus-készítését. Dönthetünk úgy, hogy ezt a jövőben nem támogatjuk. Íme néhány olyan korlátozás, amely miatt az igazítások nem támogatottak:

* Az igazítás HTML formátumú szöveghez nem érhető el, például textType=html
* Az igazítás csak a nyelvpárok egy részcsoportja esetén lesz visszaadva:
  - Az angoltól a hagyományos kínai, a kantoni (hagyományos) vagy a szerb (cirill betűs) nyelv kivételével bármely más nyelvre vagy nyelvről.
  - japánról koreaira vagy koreairól japánra.
  - Japánról egyszerűsített kínaira, kínairól egyszerűsítettről japánra. 
  - egyszerűsített kínairól hagyományos kínaira és hagyományos kínairól egyszerűsített kínaira. 
* Nem fog igazítást kapni, ha a mondat konzervfordítás. Konzervfordításra példa a "Ez egy teszt", "I love you" és más nagy gyakoriságú mondatok.
* Az igazítás nem érhető el, ha a fordítást megakadályozó megközelítéseket alkalmaz az itt leírt [módon](../prevent-translation.md)

### <a name="obtain-sentence-boundaries"></a>Mondathatárok beszerzése

A forrásszövegben és a lefordított szövegben található mondathosszról a lekérdezési sztringben adja meg `includeSentenceLength=true` a értéket.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeSentenceLength=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The answer lies in machine translation. The best machine translation technology cannot always provide translations tailored to a site or users like a human. Simply copy and paste a code snippet anywhere.'}]"
```

A válasz a következő:

```
[
    {
        "translations":[
            {
                "text":"La réponse se trouve dans la traduction automatique. La meilleure technologie de traduction automatique ne peut pas toujours fournir des traductions adaptées à un site ou des utilisateurs comme un être humain. Il suffit de copier et coller un extrait de code n’importe où.",
                "to":"fr",
                "sentLen":{"srcSentLen":[40,117,46],"transSentLen":[53,157,62]}
            }
        ]
    }
]
```

### <a name="translate-with-dynamic-dictionary"></a>Fordítás dinamikus szótár segítségével

Ha már ismeri a szóra vagy kifejezésre alkalmazni kívánt fordítást, a kérésben jelölőjelként is meg is használhatja. A dinamikus szótár csak a megfelelő főnevek, például a személyes nevek és a terméknevek esetén biztonságos.

A meg kell adni a következő szintaxist használja.

``` 
<mstrans:dictionary translation="translation of phrase">phrase</mstrans:dictionary>
```

Vegyük például a "The wordomatic is a dictionary entry" (Szószószó szótárbejegyzés) angol nyelvű mondatot. Ha meg kell őriznie _a szót_ a fordításban, küldje el a kérelmet:

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.'}]"
```

Az eredmény a következő:

```
[
    {
        "translations":[
            {"text":"Das Wort \"wordomatic\" ist ein Wörterbucheintrag.","to":"de"}
        ]
    }
]
```

Ez a funkció ugyanúgy működik a vagy a `textType=text` `textType=html` használatával. A funkciót ritkán kell használni. A fordítás testreszabásának megfelelő és sokkal jobb módja a Custom Translator. Custom Translator teljes mértékben kihasználhatja a kontextust és a statisztikai valószínűségeket. Ha van vagy megengedheti magának, hogy olyan betanító adatokat hozzon létre, amelyek kontextusban jelenítik meg a munkáját vagy kifejezését, sokkal jobb eredményeket fog kapni. [További információ a Custom Translator.](../customization.md)
