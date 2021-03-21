---
title: Fordítói fordítási módszer
titleSuffix: Azure Cognitive Services
description: Az Azure Cognitive Services Translator fordítási metódusának paramétereinek, fejlécének és szövegtörzsének megismerése szöveg fordításához.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 08/06/2020
ms.author: lajanuar
ms.openlocfilehash: d46fef0159b983f2685be40e2a0ab5471b96883b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98895441"
---
# <a name="translator-30-translate"></a>Translator 3,0: fordítás

Lefordítja a szöveget.

## <a name="request-url"></a>URL-cím kérése

Kérelem küldése `POST` a következőnek:

```HTTP
https://api.cognitive.microsofttranslator.com/translate?api-version=3.0
```

## <a name="request-parameters"></a>Kérelmek paramétereinek megadása

A lekérdezési karakterláncon átadott kérési paraméterek a következők:

### <a name="required-parameters"></a>Szükséges paraméterek

<table width="100%">
  <th width="20%">Lekérdezési paraméter</th>
  <th>Leírás</th>
  <tr>
    <td>api-verzió</td>
    <td><em>Kötelező paraméter</em>.<br/>Az ügyfél által kért API-verzió. Az értéknek a számnak kell lennie <code>3.0</code> .</td>
  </tr>
  <tr>
    <td>felhasználóként a(z)</td>
    <td><em>Kötelező paraméter</em>.<br/>Megadja a kimeneti szöveg nyelvét. A célként megadott nyelvnek a hatókörben szereplő <a href="./v3-0-languages.md">támogatott nyelvek</a> egyikének kell lennie <code>translation</code> . Például a paranccsal <code>to=de</code> németre fordítható.<br/>Egyszerre több nyelvre is lefordítható, ha megismétli a paramétert a lekérdezési karakterláncban. Például a paranccsal <code>to=de&to=it</code> németre és olaszra fordítható le.</td>
  </tr>
</table>

### <a name="optional-parameters"></a>Választható paraméterek

<table width="100%">
  <th width="20%">Lekérdezési paraméter</th>
  <th>Leírás</th>
  <tr>
    <td>a</td>
    <td>Nem <em>kötelező paraméter</em>.<br/>Megadja a bemeneti szöveg nyelvét. A hatókör használatával megkeresheti, hogy mely nyelvek érhetők el a <a href="./v3-0-languages.md">támogatott nyelvek</a> közül <code>translation</code> . Ha a <code>from</code> paraméter nincs megadva, a rendszer az automatikus nyelvfelismerés alapján határozza meg a forrás nyelvét. <br/><br/>A <code>from</code> <a href="/azure/cognitive-services/translator/dynamic-dictionary">dinamikus szótár</a> funkció használatakor az automatikus észlelés helyett a (z) paramétert kell használnia.</td>
  </tr>  
  <tr>
    <td>textType</td>
    <td>Nem <em>kötelező paraméter</em>.<br/>Meghatározza, hogy a lefordított szöveg egyszerű szöveges vagy HTML-szöveg-e. Minden HTML-fájlnak megfelelően formázott, teljes elemnek kell lennie. A lehetséges értékek a következők: <code>plain</code> (alapértelmezett) vagy <code>html</code>.</td>
  </tr>
  <tr>
    <td>category</td>
    <td>Nem <em>kötelező paraméter</em>.<br/>A fordítás kategóriáját (tartományát) megadó karakterlánc. Ezzel a paraméterrel az <a href="../customization.md">Egyéni fordítóval</a>létrehozott testreszabott rendszerből származó fordítások olvashatók be. Adja hozzá a kategória AZONOSÍTÓját az egyéni fordítói <a href="/azure/cognitive-services/translator/custom-translator/how-to-create-project#view-project-details">projekt részleteiből</a> ehhez a paraméterhez az üzembe helyezett testreszabott rendszer használatához. Az alapértelmezett érték: <code>general</code> .</td>
  </tr>
  <tr>
    <td>profanityAction</td>
    <td>Nem <em>kötelező paraméter</em>.<br/>Meghatározza, hogy a rendszer hogyan kezelje a káromkodásokat a fordításokban. A lehetséges értékek a következők: <code>NoAction</code> (alapértelmezett) <code>Marked</code> vagy <code>Deleted</code> . A káromkodás kezelésére szolgáló módszerek megismeréséhez lásd: <a href="#handle-profanity">trágárság kezelése</a>.</td>
  </tr>
  <tr>
    <td>profanityMarker</td>
    <td>Nem <em>kötelező paraméter</em>.<br/>Meghatározza, hogy a káromkodások hogyan legyenek megjelölve a fordításokban. A lehetséges értékek a következők: <code>Asterisk</code> (alapértelmezett) vagy <code>Tag</code> . A káromkodás kezelésére szolgáló módszerek megismeréséhez lásd: <a href="#handle-profanity">trágárság kezelése</a>.</td>
  </tr>
  <tr>
    <td>includeAlignment</td>
    <td>Nem <em>kötelező paraméter</em>.<br/>Azt adja meg, hogy a forrás szövegből a lefordított szöveggé való igazítási leképezést kíván-e megadni. A lehetséges értékek a következők: <code>true</code> vagy <code>false</code> (alapértelmezett). </td>
  </tr>
  <tr>
    <td>includeSentenceLength</td>
    <td>Nem <em>kötelező paraméter</em>.<br/>Meghatározza, hogy szerepeljenek-e mondatok a bemeneti szöveghez és a lefordított szöveghez. A lehetséges értékek a következők: <code>true</code> vagy <code>false</code> (alapértelmezett).</td>
  </tr>
  <tr>
    <td>suggestedFrom</td>
    <td>Nem <em>kötelező paraméter</em>.<br/>Egy tartalék nyelvet ad meg, ha a bemeneti szöveg nyelve nem azonosítható. A nyelv automatikus észlelése a <code>from</code> paraméter kihagyása esetén lesz alkalmazva. Ha az észlelés sikertelen, a <code>suggestedFrom</code> rendszer feltételezi a nyelvet.</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td>Nem <em>kötelező paraméter</em>.<br/>Megadja a bemeneti szöveg parancsfájlját.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td>Nem <em>kötelező paraméter</em>.<br/>Megadja a lefordított szöveg parancsfájlját.</td>
  </tr>
  <tr>
    <td>allowFallback</td>
    <td>Nem <em>kötelező paraméter</em>.<br/>Azt adja meg, hogy a szolgáltatás egy általános rendszer számára engedélyezett, ha nem létezik egyéni rendszer. A lehetséges értékek a következők: <code>true</code> (alapértelmezett) vagy <code>false</code> .<br/><br/><code>allowFallback=false</code> azt adja meg, hogy a fordítás csak a kérelemben meghatározott rendszerek használatára legyen kiképezve <code>category</code> . Ha az X nyelvhez való fordítás az Y nyelvhez szükséges, akkor a láncban lévő összes rendszernek (X->E és E->Y) egyéninek kell lennie, és ugyanazzal a kategóriával kell rendelkeznie. Ha nem található a megadott kategóriába tartozó rendszer, a kérelem egy 400 állapotkódot ad vissza. <code>allowFallback=true</code> azt adja meg, hogy a szolgáltatás egy általános rendszer számára engedélyezett, ha nem létezik egyéni rendszer.
</td>
  </tr>
</table> 

A kérelem fejlécei a következők:

<table width="100%">
  <th width="20%">Fejlécek</th>
  <th>Description</th>
  <tr>
    <td>Hitelesítési fejléc (ek)</td>
    <td><em>Kötelező kérelem fejléce</em><br/>Tekintse <a href="/azure/cognitive-services/translator/reference/v3-0-reference#authentication">meg a hitelesítés elérhető beállításait</a>.</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td><em>Kötelező kérelem fejléce</em><br/>Megadja az adattartalom tartalomtípusát.<br/> Az elfogadott érték: <code>application/json; charset=UTF-8</code> .</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td><em>Kötelező kérelem fejléce</em><br/>A kérelem törzsének hossza</td>
  </tr>
  <tr>
    <td>X – ClientTraceId</td>
    <td>Nem <em>kötelező</em>.<br/>Ügyfél által generált GUID a kérelem egyedi azonosításához. Kihagyhatja ezt a fejlécet, ha a lekérdezési karakterláncban szerepel a nyomkövetési azonosító a nevű lekérdezési paraméter használatával <code>ClientTraceId</code> .</td>
  </tr>
</table> 

## <a name="request-body"></a>A kérés törzse

A kérelem törzse egy JSON-tömb. Minden tömb elem egy nevű JSON `Text` -objektum, amely a fordításhoz használt karakterláncot jelöli.

```json
[
    {"Text":"I would really like to drive your car around the block a few times."}
]
```

Az alábbi korlátozások érvényesek:

* A tömb legfeljebb 100 elemet tartalmazhat.
* A kérelemben szereplő teljes szöveg nem lehet hosszabb 10 000 karakternél, beleértve a szóközöket is.

## <a name="response-body"></a>Választörzs

A sikeres válasz egy JSON-tömb, amely egyetlen eredménnyel rendelkezik a bemeneti tömb minden karakterláncához. Az eredmény objektum a következő tulajdonságokat tartalmazza:

  * `detectedLanguage`: Az észlelt nyelvet leíró objektum a következő tulajdonságokkal:

      * `language`: Az észlelt nyelv kódját jelképező karakterlánc.

      * `score`: Egy lebegőpontos érték, amely az eredmény megbízhatóságát jelzi. A pontszám nulla és egy, az alacsony pontszám pedig alacsony megbízhatóságot jelez.

    A `detectedLanguage` tulajdonság csak akkor jelenik meg az eredmény objektumban, ha a rendszer automatikus észlelést kér.

  * `translations`: A fordítási eredmények tömbje. A tömb mérete megegyezik a lekérdezési paraméterben megadott cél nyelvek számával `to` . A tömb minden eleme a következőket tartalmazza:

    * `to`: A célként megadott nyelv nyelvi kódját jelölő sztring.

    * `text`: Egy karakterlánc, amely a lefordított szöveget adja meg.

    * `transliteration`: Egy objektum, amely a paraméterben megadott parancsfájlban megadja a lefordított szöveget `toScript` .

      * `script`: A célként megadott parancsfájlt megadó karakterlánc.   

      * `text`: Egy karakterlánc, amely a lefordított szöveget adja meg a cél parancsfájlban.

    Az `transliteration` objektum nem jelenik meg, ha az írás nem történik meg.

    * `alignment`: Egy nevű, egyetlen karakterlánc-tulajdonsággal rendelkező objektum `proj` , amely leképezi a bemeneti szöveget a lefordított szövegre. Az igazítási adatok csak akkor jelennek meg, ha a kérelem paraméter `includeAlignment` értéke `true` . Az igazítás a következő formátumú karakterlánc-értékként lesz visszaadva: `[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]]` .  A kettőspont elválasztja a kezdő és a záró indexet, a kötőjel elválasztja a nyelveket, és a szóköz elválasztja a szavakat. Előfordulhat, hogy egy szó nulla, egy vagy több Szóval van igazítva a másik nyelven, és az illesztett szavak nem folytonos. Ha nincs elérhető igazítási információ, az igazítási elem üres lesz. Lásd: [igazítási információk beszerzése](#obtain-alignment-information) egy példához és korlátozásokhoz.

    * `sentLen`: Egy objektum, amely a bemeneti és a kimeneti szövegben adja vissza a mondatok határait.

      * `srcSentLen`: Egy egész tömb, amely a mondatok hosszát jelképezi a bemeneti szövegben. A tömb hossza a mondatok száma, az értékek pedig az egyes mondatok hossza.

      * `transSentLen`: Egy egész tömb, amely a mondatok hosszát jelképezi a lefordított szövegben. A tömb hossza a mondatok száma, az értékek pedig az egyes mondatok hossza.

    A mondatok határai csak abban az esetekben szerepelnek, ha a kérelem paraméter `includeSentenceLength` értéke `true` .

  * `sourceText`: Egy nevű, egyetlen karakterlánc-tulajdonsággal rendelkező objektum `text` , amely a forrás nyelvének alapértelmezett parancsfájljában megadja a bemeneti szöveget. `sourceText` a tulajdonság csak akkor jelenik meg, ha a bemenet olyan parancsfájlban van kifejezve, amely nem a nyelv szokásos parancsfájlja. Ha például a bemenet arab nyelven íródott, akkor az arab nyelvű `sourceText.text` szöveg lesz az Arab-parancsfájlba konvertálva.

Példa a JSON-válaszokra a [példák](#examples) szakaszban.

## <a name="response-headers"></a>Válaszfejlécek

<table width="100%">
  <th width="20%">Fejlécek</th>
  <th>Description</th>
    <tr>
    <td>X – kérelemazonosító</td>
    <td>A szolgáltatás által a kérelem azonosítására generált érték. Hibaelhárítási célokra szolgál.</td>
  </tr>
  <tr>
    <td>X-MT – System</td>
    <td>Meghatározza azt a rendszertípust, amelyet a fordításhoz szükséges minden egyes "to" nyelv fordításához használt. Az érték a karakterláncok vesszővel tagolt listája. Minden karakterlánc egy típust jelöl:<br/><ul><li>Az egyéni kérés magában foglal egy egyéni rendszer és legalább egy egyéni rendszer használatát a fordítás során.</li><li>Csapat – minden egyéb kérelem</li></td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Válasz-állapotkódok

A kérelem által visszaadott lehetséges HTTP-állapotkódok a következők: 

<table width="100%">
  <th width="20%">Állapotkód</th>
  <th>Description</th>
  <tr>
    <td>200</td>
    <td>Sikeres művelet.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>A lekérdezési paraméterek egyike hiányzik vagy érvénytelen. Az újrapróbálkozás előtt javítsa a kérelmek paramétereit.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>A kérést nem lehetett hitelesíteni. Győződjön meg arról, hogy a hitelesítő adatok meg vannak adva és érvényesek.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>A kérés nincs engedélyezve. Olvassa el a részletek hibaüzenetét. Ez gyakran azt jelzi, hogy a próbaverziós előfizetéssel biztosított összes ingyenes fordítás fel lett használva.</td>
  </tr>
  <tr>
    <td>408</td>
    <td>A kérés nem teljesíthető, mert hiányzik egy erőforrás. Olvassa el a részletek hibaüzenetét. Egyéni használata esetén <code>category</code> Ez gyakran azt jelzi, hogy az egyéni fordítási rendszer még nem érhető el a kérelmek kiszolgálásához. A kérést a várakozási idő (pl. 1 perc) után újra meg kell próbálni.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>A kiszolgáló elutasította a kérelmet, mert az ügyfél túllépte a kérelmek korlátait.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Váratlan hiba történt. Ha a hiba továbbra is fennáll, jelentse a következőt: a hiba dátuma és időpontja, a kérelem azonosítója a válasz fejlécből <code>X-RequestId</code> és az ügyfél azonosítója a kérelem fejlécében <code>X-ClientTraceId</code> .</td>
  </tr>
  <tr>
    <td>503</td>
    <td>A kiszolgáló átmenetileg nem érhető el. Próbálja megismételni a kérelmet. Ha a hiba továbbra is fennáll, jelentse a következőt: a hiba dátuma és időpontja, a kérelem azonosítója a válasz fejlécből <code>X-RequestId</code> és az ügyfél azonosítója a kérelem fejlécében <code>X-ClientTraceId</code> .</td>
  </tr>
</table> 

Ha hiba történik, a kérés JSON-hibaüzenetet is ad vissza. A hibakód egy 6 számjegyből álló szám, amely a 3 számjegyből álló HTTP-állapotkódot kombinálja, majd egy 3 számjegyű számot, amely további kategorizálja a hibát. Gyakori hibakódok a [v3 Translator Reference oldalon](./v3-0-reference.md#errors)találhatók. 

## <a name="examples"></a>Példák

### <a name="translate-a-single-input"></a>Egyetlen bemenet fordítása

Ebből a példából megtudhatja, hogyan fordítható le egy angolról egyszerűsített kínaira egy adott mondat.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

A válasz törzse:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    }
]
```

A `translations` tömb egyetlen elemet tartalmaz, amely a bemenetben lévő egyetlen szövegrész fordítását biztosítja.

### <a name="translate-a-single-input-with-language-auto-detection"></a>Egyetlen bemenet fordítása a nyelv automatikus észlelésével

Ebből a példából megtudhatja, hogyan fordítható le egy angolról egyszerűsített kínaira egy adott mondat. A kérelem nem adja meg a szövegbeviteli nyelvet. Ehelyett a rendszer automatikusan észleli a forrás nyelvét.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

A válasz törzse:

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
A válasz hasonló az előző példában szereplő válaszhoz. Mivel a rendszer a nyelv automatikus észlelését kérte, a válasz a bemeneti szöveghez észlelt nyelvről is tartalmaz információkat. A nyelv automatikus észlelése hatékonyabban működik a hosszú bemeneti szöveggel.

### <a name="translate-with-transliteration"></a>Fordítás az angol nyelvvel

Az előző példát kiterjesztjük az írás hozzáadásával. A következő kérelem egy latin írásrendszerben írt kínai nyelvű fordítást kér.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans&toScript=Latn" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

A válasz törzse:

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

A fordítási eredmény mostantól tartalmaz egy `transliteration` tulajdonságot, amely latin karakterek használatával adja meg a lefordított szöveget.

### <a name="translate-multiple-pieces-of-text"></a>Több darab szöveg fordítása

Ha egyszerre több karakterláncot fordít le, egyszerűen csak a karakterláncok tömbjét kell megadnia a kérelem törzsében.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}, {'Text':'I am fine, thank you.'}]"
```

A válasz az összes szövegrész fordítását tartalmazza pontosan ugyanolyan sorrendben, mint a kérelemben.
A válasz törzse:

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

Ebből a példából megtudhatja, hogyan fordíthatja le ugyanazt a bemenetet több nyelvre egy kérelemben.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

A válasz törzse:

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

### <a name="handle-profanity"></a>Káromkodás kezelése

A fordítói szolgáltatás általában megőrzi a fordításban található forrásban lévő káromkodást. A trágárság foka és az olyan kontextus, amely a szavak különböző kulturális környezetekben való megkülönböztetését teszi lehetővé, és ennek eredményeképpen a megcélzott nyelven megjelenő káromkodás mértéke felerősíthető vagy csökkenthető.

Ha el szeretné kerülni a káromkodást a fordításban, függetlenül attól, hogy a szövegben szerepel-e a káromkodás, használhatja a káromkodás szűrése lehetőséget. A beállítással megadhatja, hogy szeretné-e megtekinteni a káromkodást, hogy meg szeretné-e jelölni a káromkodásokat a megfelelő címkékkel (amely lehetővé teszi a saját feldolgozás hozzáadását), vagy ha nem szeretne műveletet végrehajtani. A `ProfanityAction` `Deleted` `Marked` `NoAction` () és az (alapértelmezett) értékek elfogadva.

<table width="100%">
  <th width="20%">ProfanityAction</th>
  <th>Művelet</th>
  <tr>
    <td><code>NoAction</code></td>
    <td>Ez az alapértelmezett viselkedés. A káromkodás a forrás és a cél között lesz továbbítva.<br/><br/>
    <strong>Példa forrása (Japán)</strong>: 彼はジャッカスです 。<br/>
    <strong>Példa fordítás (angol)</strong>: ez egy seggfej.
    </td>
  </tr>
  <tr>
    <td><code>Deleted</code></td>
    <td>A profán szavakat a rendszer a pótlás nélkül eltávolítja a kimenetből.<br/><br/>
    <strong>Példa forrása (Japán)</strong>: 彼はジャッカスです 。<br/>
    <strong>Példa fordítás (angol)</strong>: ő a.
    </td>
  </tr>
  <tr>
    <td><code>Marked</code></td>
    <td>A profán szavakat a kimenet jelölője váltja fel. A jelölő a <code>ProfanityMarker</code> paramétertől függ.<br/><br/>
A (z) esetében a profán szavakat a következőre <code>ProfanityMarker=Asterisk</code> cseréli a rendszer <code>***</code> :<br/>
    <strong>Példa forrása (Japán)</strong>: 彼はジャッカスです 。<br/>
    <strong>Példa fordítás (angol)</strong>: ő a \* \* \* .<br/><br/>
A (z) esetében a <code>ProfanityMarker=Tag</code> profán szavakat az XML-címkék &lt; profán &gt; és &lt; /profanity veszi körül &gt; :<br/>
    <strong>Példa forrása (Japán)</strong>: 彼はジャッカスです 。<br/>
    <strong>Példa fordítás (angol)</strong>: ez egy &lt; profán &gt; seggfej &lt; /profanity &gt; .
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

Összehasonlítás a rel:

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked&profanityMarker=Tag" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'This is a freaking good idea.'}]"
```

A legutóbbi kérelem visszatérési értéke:

```
[
    {
        "translations":[
            {"text":"Das ist eine <profanity>verdammt</profanity> gute Idee.","to":"de"}
        ]
    }
]
```

### <a name="translate-content-with-markup-and-decide-whats-translated"></a>Tartalom lefordítása a Markup szolgáltatással és a fordítás eldöntése

Gyakori, hogy olyan tartalmat fordítson le, amely tartalmaz egy HTML-lapról származó tartalmat vagy egy XML-dokumentum tartalmát. Lekérdezési paraméter belefoglalása `textType=html` a tartalom címkékkel való lefordításakor. Emellett időnként hasznos lehet bizonyos tartalom kizárása a fordításból. Az attribútum használatával `class=notranslate` megadhatja a tartalmat, amely az eredeti nyelven marad. Az alábbi példában az első `div` elemen belüli tartalom nem lesz lefordítva, míg a második elem tartalma le lesz `div` fordítva.

```
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

Itt látható egy példa a bemutatóra.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&textType=html" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'<div class=\"notranslate\">This will not be translated.</div><div>This will be translated.</div>'}]"
```

A válasz:

```
[
    {
        "translations":[
            {"text":"<div class=\"notranslate\">This will not be translated.</div><div>这将被翻译。</div>","to":"zh-Hans"}
        ]
    }
]
```

### <a name="obtain-alignment-information"></a>Igazítási adatok beszerzése

Az igazítás a forrás minden szavához a következő formátumú karakterlánc-értékként lesz visszaadva. Az egyes szavak adatait szóközzel elválasztva, beleértve a nem szóközzel elválasztott nyelveket (parancsfájlokat), például a következőt:

[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]] *

Példa az igazítási karakterláncra: "0:0-7:10 1:2-11:20 3:4-0:3 3:4-4:6 5:5-21:21".

Más szóval a kettőspont elkülöníti a kezdő és a záró indexet, a kötőjel elválasztja a nyelveket, és a szóköz elválasztja a szavakat. Előfordulhat, hogy egy szó nulla, egy vagy több Szóval van igazítva a másik nyelven, és az illesztett szavak nem folytonos. Ha nincs elérhető igazítási információ, az igazítási elem üres lesz. A metódus nem ad vissza hibát ebben az esetben.

Az igazítási adatok fogadásához `includeAlignment=true` a lekérdezési karakterláncban meg kell adni a következőt:.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeAlignment=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The answer lies in machine translation.'}]"
```

A válasz:

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

Az igazítási adatok a következővel kezdődnek `0:2-0:1` , ami azt jelenti, hogy a forrás szövegének első három karaktere () a `The` lefordított szöveg () első két karakterére van leképezve `La` .

#### <a name="limitations"></a>Korlátozások
Az igazítási adatok beszerzése olyan kísérleti funkció, amelyet a lehetséges kifejezés-hozzárendelésekkel kapcsolatos kutatás és tapasztalatok prototípusához engedélyezünk. Dönthet úgy, hogy a későbbiekben nem támogatja ezt a támogatást. Íme néhány olyan jelentős korlátozás, amelyben az igazítások nem támogatottak:

* Az igazítás nem érhető el HTML formátumú szöveghez, azaz textType = HTML
* Az igazítást csak a nyelvi párok egy részhalmaza adja vissza:
  - Angolról/más nyelvre, kivéve a hagyományos kínai, a kantoni (hagyományos) vagy a szerb (cirill) nyelvet.
  - Japánról koreaira vagy Koreairól Japánra.
  - Japánról kínaira egyszerűsített és kínaira egyszerűsített kínaira. 
  - Egyszerűsített kínairól kínaira, hagyományos kínairól kínaira és egyszerűsített kínaira. 
* Nem kap igazítást, ha a mondat egy konzerv fordítás. A konzerv fordítás például "Ez egy teszt", "szeretlek" és más nagy gyakoriságú mondatok.
* Az igazítás nem érhető el, ha az itt leírt módszerek valamelyikét alkalmazza a fordítás megakadályozására az [itt](../prevent-translation.md) leírtak szerint.

### <a name="obtain-sentence-boundaries"></a>Mondatok beszerzése

Ha meg szeretné kapni a mondat hosszára vonatkozó információkat a forrás szövegében és a lefordított szövegben, a `includeSentenceLength=true` lekérdezési karakterláncban kell megadni.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeSentenceLength=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The answer lies in machine translation. The best machine translation technology cannot always provide translations tailored to a site or users like a human. Simply copy and paste a code snippet anywhere.'}]"
```

A válasz:

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

### <a name="translate-with-dynamic-dictionary"></a>Fordítás dinamikus szótárral

Ha már tudja, hogy milyen fordítást szeretne alkalmazni egy szóhoz vagy kifejezésre, a kérelemben megjelölheti azt. A dinamikus szótár csak a megfelelő nevekkel, például a személyes nevekkel és a termékek nevével biztonságos.

Az ellátási kód a következő szintaxist használja.

``` 
<mstrans:dictionary translation="translation of phrase">phrase</mstrans:dictionary>
```

Tegyük fel például, hogy a "The Word wordomatic egy szótár bejegyzés" angol mondatot. Ha meg szeretné őrizni a fordítás _wordomatic_ a szót, küldje el a kérelmet:

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.'}]"
```

Az eredmény a következőket eredményezi:

```
[
    {
        "translations":[
            {"text":"Das Wort \"wordomatic\" ist ein Wörterbucheintrag.","to":"de"}
        ]
    }
]
```

Ez a funkció ugyanúgy működik, mint a vagy a szolgáltatással `textType=text` `textType=html` . A szolgáltatást takarékosan kell használni. A fordítás testreszabásának megfelelő és sokkal jobb módja az egyéni fordító használata. Az egyéni fordító a kontextus és statisztikai valószínűségek teljes körű kihasználását teszi lehetővé. Ha rendelkezik olyan képzési adataival, amelyek kontextusban mutatják be a munkát vagy kifejezést, sokkal jobb eredményeket érhet el. [További információ az egyéni fordítóról](../customization.md).