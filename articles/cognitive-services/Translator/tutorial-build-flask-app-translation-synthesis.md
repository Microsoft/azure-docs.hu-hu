---
title: 'Oktatóanyag: a Text Translator fordításához, elkészítéséhez és elemzéséhez használandó lombik-alkalmazás létrehozása'
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban egy lombik-alapú webalkalmazást fog létrehozni szöveg fordításához, a hangulat elemzéséhez és a lefordított szövegeknek a beszédbe fordításához.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: tutorial
ms.date: 03/04/2021
ms.author: lajanuar
ms.custom: devx-track-python, devx-track-js
ms.openlocfilehash: c04bac76453d565abb99a971386b9ce0461b88ae
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102172079"
---
# <a name="tutorial-build-a-flask-app-with-azure-cognitive-services"></a>Oktatóanyag: lombik-alkalmazás létrehozása az Azure Cognitive Services

Ebben az oktatóanyagban egy Azure Cognitive Servicest használó lombik-webalkalmazást fog létrehozni szöveg fordításához, a hangulat elemzéséhez és a lefordított szövegeknek a beszédbe való fordításához. A hangsúly a Python-kód és a lombikok olyan útvonalakon érhető el, amelyek lehetővé teszik az alkalmazás, de segítünk Önnek a HTML és a JavaScript, amely az alkalmazást együtt kéri le. Ha bármilyen problémába ütközik, tudassa velünk az alábbi visszajelzési gomb használatával.

Az oktatóanyag a következőket ismerteti:

> [!div class="checklist"]
> * Azure-előfizetési kulcsok beszerzése
> * A fejlesztési környezet beállítása és a függőségek telepítése
> * Lombik-alkalmazás létrehozása
> * A fordító használata szöveg fordításához
> * A Text Analytics használatával elemezheti a bemeneti szöveg és a fordítások pozitív/negatív hangulatát
> * Lefordított szöveg átalakítása szintetizált beszédre a Speech Services használatával
> * A lombik alkalmazás helyi futtatása

> [!TIP]
> Ha továbbra is szeretné kihagyni az összes kódot, akkor a teljes minta, valamint a Build utasítások a [githubon](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial)érhetők el.

## <a name="what-is-flask"></a>Mi az a Flask?

A lombik a webalkalmazások létrehozásához használható keretrendszer. Ez azt jelenti, hogy a lombik olyan eszközöket, kódtárakat és technológiákat biztosít, amelyek lehetővé teszik egy webalkalmazás összeállítását. Ez a webalkalmazás lehet néhány weblap, egy blog, egy wiki vagy egy webalapú naptári alkalmazás vagy egy kereskedelmi webhely.

Azok számára, akik részletesen szeretnék eljutni az oktatóanyag után, néhány hasznos hivatkozást talál:

* [A lombik dokumentációja](http://flask.pocoo.org/)
* [A lombik a kezdőknek – kezdő útmutató a lombikhoz](https://codeburst.io/flask-for-dummies-a-beginners-guide-to-flask-part-uno-53aec6afc5b1)

## <a name="prerequisites"></a>Előfeltételek

Tekintsük át az oktatóanyaghoz szükséges szoftvereket és előfizetési kulcsokat.

* [Python 3.5.2 vagy újabb](https://www.python.org/downloads/)
* [Git-eszközök](https://git-scm.com/downloads)
* IDE-vagy szövegszerkesztő, például a [Visual Studio Code](https://code.visualstudio.com/) vagy az [Atom](https://atom.io/)  
* [Chrome](https://www.google.com/chrome/browser/) vagy [Firefox](https://www.mozilla.org/firefox)
* Egy **Translator** előfizetési kulcs (a **globális** helyet valószínűleg használhatja.)
* **Text Analytics** előfizetési kulcs az **USA nyugati** régiójában.
* Egy **Speech Services** -előfizetési kulcs az **USA nyugati** régiójában.

## <a name="create-an-account-and-subscribe-to-resources"></a>Hozzon létre egy fiókot, és fizessen elő az erőforrásokra

Ahogy korábban említettük, három előfizetési kulcsra lesz szüksége ehhez az oktatóanyaghoz. Ez azt jelenti, hogy létre kell hoznia egy erőforrást az Azure-fiókjában a következőkhöz:
* Translator
* Text Analytics
* Beszédszolgáltatások

Erőforrások létrehozásához használja [a Cognitive Services fiók létrehozása a Azure Portalban](../cognitive-services-apis-create-account.md) című témakört.

> [!IMPORTANT]
> Ebben az oktatóanyagban hozza létre az erőforrásokat az USA nyugati régiójában. Ha más régiót használ, minden Python-fájlban módosítania kell az alap URL-címet.

## <a name="set-up-your-dev-environment"></a>A fejlesztési környezet beállítása

A lombik-Webalkalmazás létrehozása előtt létre kell hoznia egy munkakönyvtárat a projekthez, és telepítenie kell néhány Python-csomagot.

### <a name="create-a-working-directory"></a>Munkakönyvtár létrehozása

1. Nyissa meg a parancssort (Windows) vagy a terminált (macOS/Linux). Ezután hozzon létre egy munkakönyvtárat és alkönyvtárakat a projekthez:  

   ```
   mkdir -p flask-cog-services/static/scripts && mkdir flask-cog-services/templates
   ```
2. Váltás a projekt munkakönyvtárára:  

   ```
   cd flask-cog-services
   ```

### <a name="create-and-activate-your-virtual-environment-with-virtualenv"></a>Virtuális környezet létrehozása és aktiválása a `virtualenv`

Hozzunk létre egy virtuális környezetet a lombik-alkalmazáshoz a használatával `virtualenv` . A virtuális környezet használatával gondoskodhat arról, hogy tiszta környezetet biztosítson a működéséhez.

1. A munkakönyvtárában futtassa ezt a parancsot egy virtuális környezet létrehozásához: **MacOS/Linux:**
   ```
   virtualenv venv --python=python3
   ```
   Kifejezetten kijelentette, hogy a virtuális környezetnek a Python 3 használatát kell használnia. Ez biztosítja, hogy a több Python-telepítéssel rendelkező felhasználók a megfelelő verziót használják.

   **Windows CMD/Windows bash:**
   ```
   virtualenv venv
   ```
   A dolgok egyszerűsége érdekében a virtuális környezet venv nevezzük.

2. A virtuális környezet aktiválásához szükséges parancsok a platformtól/rendszerhéjtól függően eltérőek lesznek:   

   | Platform | Felület | Parancs |
   |----------|-------|---------|
   | macOS/Linux | bash/zsh | `source venv/bin/activate` |
   | Windows | bash | `source venv/Scripts/activate` |
   | | Parancssor | `venv\Scripts\activate.bat` |
   | | PowerShell | `venv\Scripts\Activate.ps1` |

   A parancs futtatása után a parancssori vagy a terminál-munkamenetnek a következővel kell megjelennie: `venv` .

3. A munkamenetet bármikor inaktiválhatja úgy, hogy beírja a következőt a parancssorba vagy a terminálba: `deactivate` .

> [!NOTE]
> A Python kiterjedt dokumentációval rendelkezik a virtuális környezetek létrehozásához és kezeléséhez: [virtualenv](https://virtualenv.pypa.io/en/latest/).

### <a name="install-requests"></a>Telepítési kérelmek

A kérelmek egy népszerű modul, amely HTTP 1,1-kérelmek küldésére szolgál. A lekérdezési karakterláncokat nem kell manuálisan hozzáadnia az URL-címekhez, vagy pedig a POST-kódolást.

1. A kérelmek telepítéséhez futtassa a következőt:

   ```
   pip install requests
   ```

> [!NOTE]
> Ha többet szeretne megtudni a kérelmekről, tekintse meg [a kérelmek: http for Humans](https://2.python-requests.org/en/master/)című témakört.

### <a name="install-and-configure-flask"></a>A lombik telepítése és konfigurálása

Ezután telepítenie kell a lombikot. A lombik kezeli a webalkalmazáshoz tartozó útválasztást, és lehetővé teszi, hogy a kiszolgáló és a kiszolgáló közötti hívásokat elrejtse az előfizetési kulcsokat a végfelhasználótól.

1. A lombik telepítéséhez futtassa a következőt:
   ```
   pip install Flask
   ```
   Győződjön meg arról, hogy a lombik telepítve lett. Futtassa a következőt:
   ```
   flask --version
   ```
   A verziót ki kell nyomtatni a terminálba. Bármi más, ami hibás volt.

2. A lombik alkalmazás futtatásához használja a lombik parancsát vagy a Python-m kapcsolót a lombik használatával. Mielőtt ezt elvégezte, meg kell adnia a terminálnak, hogy melyik alkalmazás működjön együtt a `FLASK_APP` környezeti változó exportálásával:

   **MacOS/Linux**:
   ```
   export FLASK_APP=app.py
   ```

   **Windows**:
   ```
   set FLASK_APP=app.py
   ```

## <a name="create-your-flask-app"></a>A lombik alkalmazás létrehozása

Ebben a szakaszban egy olyan barebone lombik-alkalmazást fog létrehozni, amely egy HTML-fájlt ad vissza, amikor a felhasználók megtalálják az alkalmazás gyökerét. Ne töltsön túl sok időt a kód kiválasztására, ezért később ismét frissíteni fogjuk a fájlt.

### <a name="what-is-a-flask-route"></a>Mi az a lombik-útvonal?

Lássunk egy percet, hogy beszéljünk az "[útvonalakról](http://flask.pocoo.org/docs/1.0/api/#flask.Flask.route)". Az Útválasztás egy adott függvény URL-címének kötésére szolgál. A lombik Route dekoratőr használatával regisztrálja a függvényeket adott URL-címekre. Ha például egy felhasználó a webalkalmazás gyökerére () navigál `/` , a `index.html` rendszer megjeleníti.  

```python
@app.route('/')
def index():
    return render_template('index.html')
```

Lássunk még egy példát, hogy megismerkedjen a kezdőlapon.

```python
@app.route('/about')
def about():
    return render_template('about.html')
```

Ez a kód biztosítja, hogy amikor egy felhasználó navigál a `http://your-web-app.com/about` `about.html` fájl megjelenítéséhez,

Habár ezek a minták bemutatják, hogyan lehet HTML-lapokat megjeleníteni egy felhasználó számára, az útvonalak az API-k meghívására is használhatók gomb lenyomásakor, vagy tetszőleges számú művelet elvégzése anélkül, hogy el kellene érni a kezdőlapot. Ez a művelet akkor jelenik meg, ha útvonalakat hoz létre a fordításhoz, a hangulathoz és a beszédfelismeréshez.

### <a name="get-started"></a>Bevezetés

1. Nyissa meg a projektet az IDE-ben, majd hozzon létre egy nevű fájlt `app.py` a munkakönyvtár gyökerében. Ezután másolja be a kódot a következőre, `app.py` és mentse:

   ```python
   from flask import Flask, render_template, url_for, jsonify, request

   app = Flask(__name__)
   app.config['JSON_AS_ASCII'] = False

   @app.route('/')
   def index():
       return render_template('index.html')
   ```

   Ez a kódrészlet azt jelzi, hogy az alkalmazás `index.html` minden alkalommal megjelenik, amikor a felhasználó a webalkalmazás gyökerére navigál ( `/` ).

2. Ezután hozzuk létre a webes alkalmazás előtér-szolgáltatását. Hozzon létre egy nevű fájlt `index.html` a `templates` címtárban. Ezután másolja be a kódot a alkalmazásba `templates/index.html` .

   ```html
   <!doctype html>
   <html lang="en">
     <head>
       <!-- Required metadata tags -->
       <meta charset="utf-8">
       <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
       <meta name="description" content="Translate and analyze text with Azure Cognitive Services.">
       <!-- Bootstrap CSS -->
       <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
       <title>Translate and analyze text with Azure Cognitive Services</title>
     </head>
     <body>
       <div class="container">
         <h1>Translate, synthesize, and analyze text with Azure</h1>
         <p>This simple web app uses Azure for text translation, text-to-speech conversion, and sentiment analysis of input text and translations. Learn more about <a href="https://docs.microsoft.com/azure/cognitive-services/">Azure Cognitive Services</a>.
        </p>
        <!-- HTML provided in the following sections goes here. -->

        <!-- End -->
       </div>

       <!-- Required Javascript for this tutorial -->
       <script src="https://code.jquery.com/jquery-3.2.1.slim.min.js" integrity="sha384-KJ3o2DKtIkvYIK3UENzmM7KCkRr/rE9/Qpg6aAZGJwFDMVNA/GpGFF93hXpG5KkN" crossorigin="anonymous"></script>
       <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
       <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
       <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous"></script>
       <script type = "text/javascript" src ="static/scripts/main.js"></script>
     </body>
   </html>
   ```

3. Vizsgáljuk meg a lombik alkalmazást. A terminálból futtassa a következőt:

   ```
   flask run
   ```

4. Nyisson meg egy böngészőt, és navigáljon a megadott URL-címhez. Ekkor meg kell jelennie az egyoldalas alkalmazásnak. Nyomja le a **CTRL + C** billentyűkombinációt az alkalmazás leöléséhez.

## <a name="translate-text"></a>Szöveg lefordítása

Most, hogy már van egy ötlete arról, hogyan működik egy egyszerű lombik alkalmazás, lássuk:

* Néhány Python írása a fordító meghívására és válasz visszaküldésére
* Lombik-útvonal létrehozása a Python-kód meghívásához
* A HTML-fájl frissítése egy olyan résszel, amely szövegbeviteli és fordítási, nyelvi választó és fordítási gomb
* JavaScript írása, amely lehetővé teszi a felhasználók számára, hogy a HTML-ből használhassák a lombik alkalmazását

### <a name="call-the-translator"></a>A fordító meghívása

Az első szükséges, hogy egy függvényt írjon a fordító meghívásához. Ez a függvény két argumentumot fogad: `text_input` és `language_output` . Ezt a függvényt akkor hívja meg a rendszer, amikor egy felhasználó megnyomja az alkalmazás fordítási gombját. A HTML-ben a szöveg terület lesz elküldve `text_input` , a HTML-ben pedig a nyelv kiválasztási értéke lesz `language_output` .

1. Kezdjük azzal, hogy létrehozunk egy nevű fájlt `translate.py` a munkakönyvtár gyökerében.
2. Ezután adja hozzá ezt a kódot a következőhöz: `translate.py` . Ez a függvény két argumentumot vesz igénybe: `text_input` és `language_output` .
   ```python
   import os, requests, uuid, json

   # Don't forget to replace with your Cog Services subscription key!
   # If you prefer to use environment variables, see Extra Credit for more info.
   subscription_key = 'YOUR_TRANSLATOR_TEXT_SUBSCRIPTION_KEY'
   location = 'YOUR_TRANSLATOR_RESOURCE_LOCATION'
   # Don't forget to replace with your Cog Services location!
   # Our Flask route will supply two arguments: text_input and language_output.
   # When the translate text button is pressed in our Flask app, the Ajax request
   # will grab these values from our web app, and use them in the request.
   # See main.js for Ajax calls.
   def get_translation(text_input, language_output):
       base_url = 'https://api.cognitive.microsofttranslator.com'
       path = '/translate?api-version=3.0'
       params = '&to=' + language_output
       constructed_url = base_url + path + params

       headers = {
           'Ocp-Apim-Subscription-Key': subscription_key,
           'Ocp-Apim-Subscription-Region': location,
           'Content-type': 'application/json',
           'X-ClientTraceId': str(uuid.uuid4())
       }

       # You can pass more than one object in body.
       body = [{
           'text' : text_input
       }]
       response = requests.post(constructed_url, headers=headers, json=body)
       return response.json()
   ```
3. Adja hozzá a Translator előfizetés kulcsát, és mentse.

### <a name="add-a-route-to-apppy"></a>Útvonal hozzáadása a következőhöz `app.py`

Ezután létre kell hoznia egy útvonalat a lombik alkalmazásban, amely meghívja a-t `translate.py` . Ez az útvonal akkor lesz meghívva, amikor egy felhasználó megnyomja az alkalmazás fordítás gombját.

Ehhez az alkalmazáshoz az útvonal fogadja a `POST` kérelmeket. Ennek az az oka, hogy a függvény lefordítja a szöveget, és a fordításhoz egy kimeneti nyelvet vár.

A lombik segítő függvények segítséget nyújtanak az egyes kérések elemzéséhez és kezeléséhez. A megadott kódban a `get_json()` kérelem JSON-ként adja vissza az adatait `POST` . Ezután `data['text']` a és `data['to']` a használatával a szöveges és a kimeneti nyelvi értékeket adja át a `get_translation()` függvénynek `translate.py` . Az utolsó lépés a válasz JSON-ként való visszaküldése, mivel ezeket az adattartalmat a webalkalmazásban kell megjeleníteni.

A következő részekben ezt a folyamatot fogja megismételni, ahogy az adatelemzési és-beszédfelismerési útvonalakat hoz létre.

1. Nyissa meg `app.py` és keresse meg az importálási utasítást a tetején, `app.py` és adja hozzá a következő sort:

   ```python
   import translate
   ```
   A lombik-alkalmazás most már a által elérhető módszert is használhatja `translate.py` .

2. Másolja ezt a kódot a végére, és mentse a következőt `app.py` :

   ```python
   @app.route('/translate-text', methods=['POST'])
   def translate_text():
       data = request.get_json()
       text_input = data['text']
       translation_output = data['to']
       response = translate.get_translation(text_input, translation_output)
       return jsonify(response)
   ```

### <a name="update-indexhtml"></a>A `index.html` frissítése

Most, hogy van egy függvény a szöveg fordításához, és egy útvonal a lombik alkalmazásban való meghívásához, a következő lépés az alkalmazás HTML-címének létrehozása. Az alábbi HTML-fájl néhány dolgot tartalmaz:

* Egy szöveges területeket biztosít, ahol a felhasználók beírhatják a szöveget a fordításhoz.
* Nyelvi választót tartalmaz.
* HTML-elemeket tartalmaz a fordítás során visszaadott észlelt nyelv és megbízhatósági pontszámok megjelenítéséhez.
* Egy írásvédett szövegmezőt biztosít, ahol a fordítási kimenet megjelenik.
* Az oktatóanyagban később a fájlhoz felvenni kívánt, a hangulat elemzéséhez és a Speech szintézishez használt helyőrzőket tartalmazza.

Frissítsen `index.html` .

1. Nyissa meg `index.html` a következő kódot, és keresse meg a megjegyzéseket:
   ```html
   <!-- HTML provided in the following sections goes here. -->

   <!-- End -->
   ```

2. A kód megjegyzéseit cserélje le a következő HTML-blokkra:
   ```html
   <div class="row">
     <div class="col">
       <form>
         <!-- Enter text to translate. -->
         <div class="form-group">
           <label for="text-to-translate"><strong>Enter the text you'd like to translate:</strong></label>
           <textarea class="form-control" id="text-to-translate" rows="5"></textarea>
         </div>
         <!-- Select output language. -->
         <div class="form-group">
           <label for="select-language"><strong>Translate to:</strong></label>
           <select class="form-control" id="select-language">
             <option value="ar">Arabic</option>
             <option value="ca">Catalan</option>
             <option value="zh-Hans">Chinese (Simplified)</option>
             <option value="zh-Hant">Chinese (Traditional)</option>
             <option value="hr">Croatian</option>
             <option value="en">English</option>
             <option value="fr">French</option>
             <option value="de">German</option>
             <option value="el">Greek</option>
             <option value="he">Hebrew</option>
             <option value="hi">Hindi</option>
             <option value="it">Italian</option>
             <option value="ja">Japanese</option>
             <option value="ko">Korean</option>
             <option value="pt">Portuguese</option>
             <option value="ru">Russian</option>
             <option value="es">Spanish</option>
             <option value="th">Thai</option>
             <option value="tr">Turkish</option>
             <option value="vi">Vietnamese</option>
           </select>
         </div>
         <button type="submit" class="btn btn-primary mb-2" id="translate">Translate text</button></br>
         <div id="detected-language" style="display: none">
           <strong>Detected language:</strong> <span id="detected-language-result"></span><br />
           <strong>Detection confidence:</strong> <span id="confidence"></span><br /><br />
         </div>

         <!-- Start sentiment code-->

         <!-- End sentiment code -->

       </form>
     </div>
     <div class="col">
       <!-- Translated text returned by the Translate API is rendered here. -->
       <form>
         <div class="form-group" id="translator-text-response">
           <label for="translation-result"><strong>Translated text:</strong></label>
           <textarea readonly class="form-control" id="translation-result" rows="5"></textarea>
         </div>

         <!-- Start voice font selection code -->

         <!-- End voice font selection code -->

       </form>

       <!-- Add Speech Synthesis button and audio element -->

       <!-- End Speech Synthesis button -->

     </div>
   </div>
   ```

A következő lépés a JavaScript írása. Ez a híd a HTML és a lombik útvonala között.

### <a name="create-mainjs"></a>Létrehozása `main.js`  

A `main.js` fájl a HTML-és a lombik-útvonal közötti híd. Az alkalmazás a jQuery, Ajax és XMLHttpRequest kombinációját fogja használni a tartalom megjelenítéséhez, és `POST` kéréseket tesz a lombik-útvonalakhoz.

Az alábbi kódban a HTML-ből származó tartalmat használjuk a lombik útvonalára vonatkozó kérelem létrehozásához. Pontosabban a szövegmező tartalmát és a Nyelvi választót a rendszer a változóhoz rendeli hozzá, majd a kérelemben a következőre küldi át: `translate-text` .

A kód ezután megismétli a választ, és frissíti a HTML-t a fordítás, az észlelt nyelv és a megbízhatósági pontszám alapján.

1. Az IDE-ből hozzon létre egy nevű fájlt `main.js` a `static/scripts` címtárban.
2. A kód másolása a következőbe `static/scripts/main.js` :
   ```javascript
   //Initiate jQuery on load.
   $(function() {
     //Translate text with flask route
     $("#translate").on("click", function(e) {
       e.preventDefault();
       var translateVal = document.getElementById("text-to-translate").value;
       var languageVal = document.getElementById("select-language").value;
       var translateRequest = { 'text': translateVal, 'to': languageVal }

       if (translateVal !== "") {
         $.ajax({
           url: '/translate-text',
           method: 'POST',
           headers: {
               'Content-Type':'application/json'
           },
           dataType: 'json',
           data: JSON.stringify(translateRequest),
           success: function(data) {
             for (var i = 0; i < data.length; i++) {
               document.getElementById("translation-result").textContent = data[i].translations[0].text;
               document.getElementById("detected-language-result").textContent = data[i].detectedLanguage.language;
               if (document.getElementById("detected-language-result").textContent !== ""){
                 document.getElementById("detected-language").style.display = "block";
               }
               document.getElementById("confidence").textContent = data[i].detectedLanguage.score;
             }
           }
         });
       };
     });
     // In the following sections, you'll add code for sentiment analysis and
     // speech synthesis here.
   })
   ```

### <a name="test-translation"></a>Teszt fordítás

Tesztelje a fordítást az alkalmazásban.

```
flask run
```

Navigáljon a megadott kiszolgálócím-címre. Írja be a szövegbeviteli terület szövegét, válassza ki a nyelvet, majd nyomja le a fordítás gombot. Be kell szereznie egy fordítást. Ha nem működik, győződjön meg arról, hogy az előfizetési kulcsot adta hozzá.

> [!TIP]
> Ha a végrehajtott módosítások nem jelennek meg, vagy az alkalmazás nem a várt módon működik, próbálja meg törölni a gyorsítótárat, vagy nyisson meg egy privát/inkognitóban ablakot.

Nyomja le a **CTRL + c** billentyűkombinációt az alkalmazás leöléséhez, majd a következő szakaszhoz.

## <a name="analyze-sentiment"></a>Vélemények elemzése

A [text Analytics API](../text-analytics/overview.md) az érzelmek elemzéséhez, a szövegből kinyert fő kifejezésekhez vagy a forrás nyelvének észleléséhez használható. Ebben az alkalmazásban az érzelmek elemzését fogjuk használni annak megállapításához, hogy a megadott szöveg pozitív, semleges vagy negatív. Az API egy 0 és 1 közötti számértéket ad vissza. Az 1. ponthoz közeledő pontszámok pozitív és negatív eredményt jeleznek.

Ebben a szakaszban néhány dolgot kell tennie:

* Írjon egy Pythont, hogy meghívja a Text Analytics API az érzelmek elemzéséhez és a válasz visszaküldéséhez.
* Lombik-útvonal létrehozása a Python-kód meghívásához
* A HTML-fájl frissítése egy, a hangulati pontszámokat tartalmazó résszel és egy, az elemzést elvégző gomb
* JavaScript írása, amely lehetővé teszi a felhasználók számára, hogy a HTML-ből használhassák a lombik alkalmazását

### <a name="call-the-text-analytics-api"></a>Szövegelemzési API hívása

Írjon egy függvényt a Text Analytics API meghívásához. A függvény négy argumentumot fogad: `input_text` , `input_language` , `output_text` és `output_language` . Ezt a függvényt akkor hívja meg a rendszer, amikor egy felhasználó megnyomja az alkalmazásban az érzelmi elemzés futtatása gombot. A felhasználó által a szövegmezőből és a nyelvi választóból biztosított adatok, valamint az észlelt nyelv és a fordítási kimenet minden kérelem esetében elérhető. A válasz objektum a forrás és a fordítás hangulati pontszámait tartalmazza. A következő részekben írni fog néhány JavaScriptet, hogy elemezze a választ, és használja azt az alkalmazásban. Egyelőre a Text Analytics API hívására koncentrálunk.

1. Hozzon létre egy nevű fájlt `sentiment.py` a munkakönyvtár gyökerében.
2. Ezután adja hozzá ezt a kódot a következőhöz: `sentiment.py` .
   ```python
   import os, requests, uuid, json

   # Don't forget to replace with your Cog Services subscription key!
   subscription_key = 'YOUR_TEXT_ANALYTICS_SUBSCRIPTION_KEY'
   endpoint = "YOUR_TEXT_ANALYTICS_ENDPOINT" 
   # Our Flask route will supply four arguments: input_text, input_language,
   # output_text, output_language.
   # When the run sentiment analysis button is pressed in our Flask app,
   # the Ajax request will grab these values from our web app, and use them
   # in the request. See main.js for Ajax calls.

   def get_sentiment(input_text, input_language):
       path = '/text/analytics/v3.0/sentiment'
       constructed_url = endpoint + path

       headers = {
           'Ocp-Apim-Subscription-Key': subscription_key,
           'Content-type': 'application/json',
           'X-ClientTraceId': str(uuid.uuid4())
       }

       # You can pass more than one object in body.
       body = {
           'documents': [
               {
                   'language': input_language,
                   'id': '1',
                   'text': input_text
               },
           ]
       }
       response = requests.post(constructed_url, headers=headers, json=body)
       return response.json()
   ```
3. Adja hozzá Text Analytics előfizetési kulcsát, és mentse.

### <a name="add-a-route-to-apppy"></a>Útvonal hozzáadása a következőhöz `app.py`

Hozzon létre egy útvonalat a lombik alkalmazásban, amely meghívja a-t `sentiment.py` . Ez az útvonal akkor lesz meghívva, amikor egy felhasználó megnyomja az alkalmazásban az érzelmi elemzés futtatása gombot. A fordítási útvonalhoz hasonlóan ez az útvonal fogadja a `POST` kéréseket, mivel a függvény argumentumokat vár.

1. Nyissa meg `app.py` és keresse meg az importálási utasítást a tetején, és frissítse a következőt `app.py` :

   ```python
   import translate, sentiment
   ```
   A lombik-alkalmazás most már a által elérhető módszert is használhatja `sentiment.py` .

2. Másolja ezt a kódot a végére, és mentse a következőt `app.py` :
   ```python
   @app.route('/sentiment-analysis', methods=['POST'])
   def sentiment_analysis():
       data = request.get_json()
       input_text = data['inputText']
       input_lang = data['inputLanguage']
       response = sentiment.get_sentiment(input_text, input_lang)
       return jsonify(response)
   ```

### <a name="update-indexhtml"></a>A `index.html` frissítése

Most, hogy már rendelkezik egy, a hangulat-elemzés futtatására szolgáló függvénnyel, és egy útvonal a lombik alkalmazásban a hívásához, a következő lépés az alkalmazás HTML-fájljának megírása. Az alábbi HTML-fájl néhány dolgot tartalmaz:

* Egy gomb hozzáadásával az alkalmazáshoz az érzelmek elemzésének futtatásához
* Egy olyan elemet ad meg, amely a hangulati pontozást ismerteti
* Egy elem hozzáadásával jelenítheti meg az érzelmek pontszámait

1. Nyissa meg `index.html` a következő kódot, és keresse meg a megjegyzéseket:
   ```html
   <!-- Start sentiment code-->

   <!-- End sentiment code -->
   ```

2. A kód megjegyzéseit cserélje le a következő HTML-blokkra:
   ```html
   <button type="submit" class="btn btn-primary mb-2" id="sentiment-analysis">Run sentiment analysis</button></br>
   <div id="sentiment" style="display: none">
      <p>Sentiment can be labeled as "positive", "negative", "neutral", or "mixed". </p>
      <strong>Sentiment label for input:</strong> <span id="input-sentiment"></span><br />
   </div>
   ```

### <a name="update-mainjs"></a>A `main.js` frissítése

Az alábbi kódban a HTML-ből származó tartalmat használjuk a lombik útvonalára vonatkozó kérelem létrehozásához. Pontosabban a szövegmező tartalmát és a Nyelvi választót a rendszer a változókhoz rendeli hozzá, majd az útvonalra irányuló kérésben továbbítja azokat `sentiment-analysis` .

A kód ezután megismétli a választ, és frissíti a HTML-t az érzelmi pontszámokkal.

1. Az IDE-ből hozzon létre egy nevű fájlt `main.js` a `static` címtárban.

2. A kód másolása a következőbe `static/scripts/main.js` :
   ```javascript
   //Run sentiment analysis on input and translation.
   $("#sentiment-analysis").on("click", function(e) {
     e.preventDefault();
     var inputText = document.getElementById("text-to-translate").value;
     var inputLanguage = document.getElementById("detected-language-result").innerHTML;
     var outputText = document.getElementById("translation-result").value;
     var outputLanguage = document.getElementById("select-language").value;

     var sentimentRequest = { "inputText": inputText, "inputLanguage": inputLanguage};

     if (inputText !== "") {
       $.ajax({
         url: "/sentiment-analysis",
         method: "POST",
         headers: {
             "Content-Type":"application/json"
         },
         dataType: "json",
         data: JSON.stringify(sentimentRequest),
         success: function(data) {
           for (var i = 0; i < data.documents.length; i++) {
             if (typeof data.documents[i] !== "undefined"){
               if (data.documents[i].id === "1") {
                 document.getElementById("input-sentiment").textContent = data.documents[i].sentiment;
               }
             }
           }
           for (var i = 0; i < data.errors.length; i++) {
             if (typeof data.errors[i] !== "undefined"){
               if (data.errors[i].id === "1") {
                 document.getElementById("input-sentiment").textContent = data.errors[i].message;
               }
             }
           }
           if (document.getElementById("input-sentiment").textContent !== ''){
             document.getElementById("sentiment").style.display = "block";
           }
         }
       });
     }
   });
   // In the next section, you'll add code for speech synthesis here.
   ```

### <a name="test-sentiment-analysis"></a>Tesztelési hangulat elemzése

Tesztelje az alkalmazás hangulati elemzését.

```
flask run
```

Navigáljon a megadott kiszolgálócím-címre. Írja be a szövegbeviteli terület szövegét, válassza ki a nyelvet, majd nyomja le a fordítás gombot. Be kell szereznie egy fordítást. Ezután nyomja meg a Run hangulat elemzése gombot. Két pontszámnak kell megjelennie. Ha nem működik, győződjön meg arról, hogy az előfizetési kulcsot adta hozzá.

> [!TIP]
> Ha a végrehajtott módosítások nem jelennek meg, vagy az alkalmazás nem a várt módon működik, próbálja meg törölni a gyorsítótárat, vagy nyisson meg egy privát/inkognitóban ablakot.

Nyomja le a **CTRL + c** billentyűkombinációt az alkalmazás leöléséhez, majd a következő szakaszhoz.

## <a name="convert-text-to-speech"></a>Szöveg átalakítása beszéddé

A [text-to-SPEECH API](../speech-service/text-to-speech.md) lehetővé teszi, hogy az alkalmazás a szöveget természetes emberi – például szintetizált beszédre alakítsa át. A szolgáltatás támogatja a standard, a neurális és az egyéni hangokat. A minta alkalmazásunk egy maroknyi elérhető hangokat használ a teljes listához: [támogatott nyelvek](../speech-service/language-support.md#text-to-speech).

Ebben a szakaszban néhány dolgot kell tennie:

* Írjon egy Pythont a szöveg-beszéd átalakításra a Text-to-Speech API-val
* Lombik-útvonal létrehozása a Python-kód meghívásához
* Frissítse a HTML-t egy gombbal a szöveg és a beszéd átalakításához, és egy elemet a hanglejátszáshoz
* JavaScript írása, amely lehetővé teszi a felhasználók számára a lombik alkalmazással való interakciót

### <a name="call-the-text-to-speech-api"></a>A szöveg és a beszéd közötti API meghívása

Írjunk egy függvényt szöveg-beszéd átalakításra. Ez a függvény két argumentumot fogad: `input_text` és `voice_font` . Ezt a függvényt akkor hívja meg a rendszer, amikor egy felhasználó megnyomja az alkalmazás szöveg-beszéd konvertálása gombját. `input_text` a szöveg lefordítására irányuló hívás által visszaadott fordítási kimenet a `voice_font` HTML-ben a hangbetűkészletek választójának értéke.

1. Hozzon létre egy nevű fájlt `synthesize.py` a munkakönyvtár gyökerében.

2. Ezután adja hozzá ezt a kódot a következőhöz: `synthesize.py` .
   ```Python
   import os, requests, time
   from xml.etree import ElementTree

   class TextToSpeech(object):
       def __init__(self, input_text, voice_font):
           subscription_key = 'YOUR_SPEECH_SERVICES_SUBSCRIPTION_KEY'
           self.subscription_key = subscription_key
           self.input_text = input_text
           self.voice_font = voice_font
           self.timestr = time.strftime('%Y%m%d-%H%M')
           self.access_token = None

       # This function performs the token exchange.
       def get_token(self):
           fetch_token_url = 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken'
           headers = {
               'Ocp-Apim-Subscription-Key': self.subscription_key
           }
           response = requests.post(fetch_token_url, headers=headers)
           self.access_token = str(response.text)

       # This function calls the TTS endpoint with the access token.
       def save_audio(self):
           base_url = 'https://westus.tts.speech.microsoft.com/'
           path = 'cognitiveservices/v1'
           constructed_url = base_url + path
           headers = {
               'Authorization': 'Bearer ' + self.access_token,
               'Content-Type': 'application/ssml+xml',
               'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
               'User-Agent': 'YOUR_RESOURCE_NAME',
           }
           # Build the SSML request with ElementTree
           xml_body = ElementTree.Element('speak', version='1.0')
           xml_body.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-us')
           voice = ElementTree.SubElement(xml_body, 'voice')
           voice.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-US')
           voice.set('name', 'Microsoft Server Speech Text to Speech Voice {}'.format(self.voice_font))
           voice.text = self.input_text
           # The body must be encoded as UTF-8 to handle non-ascii characters.
           body = ElementTree.tostring(xml_body, encoding="utf-8")

           #Send the request
           response = requests.post(constructed_url, headers=headers, data=body)

           # Write the response as a wav file for playback. The file is located
           # in the same directory where this sample is run.
           return response.content
   ```
3. Adja hozzá a Speech Services-előfizetési kulcsot, és mentse.

### <a name="add-a-route-to-apppy"></a>Útvonal hozzáadása a következőhöz `app.py`

Hozzon létre egy útvonalat a lombik alkalmazásban, amely meghívja a-t `synthesize.py` . Ez az útvonal akkor lesz meghívva, amikor egy felhasználó megnyomja az alkalmazás szöveg-beszéd konvertálása gombját. A fordítási és a hangulati elemzési útvonalakhoz hasonlóan ez az útvonal fogadja a `POST` kéréseket, mivel a függvény két argumentumot vár: a szintetizált szöveg és a lejátszás hangbetűkészlete.

1. Nyissa meg `app.py` és keresse meg az importálási utasítást a tetején, és frissítse a következőt `app.py` :

   ```python
   import translate, sentiment, synthesize
   ```
   A lombik-alkalmazás most már a által elérhető módszert is használhatja `synthesize.py` .

2. Másolja ezt a kódot a végére, és mentse a következőt `app.py` :

   ```Python
   @app.route('/text-to-speech', methods=['POST'])
   def text_to_speech():
       data = request.get_json()
       text_input = data['text']
       voice_font = data['voice']
       tts = synthesize.TextToSpeech(text_input, voice_font)
       tts.get_token()
       audio_response = tts.save_audio()
       return audio_response
   ```

### <a name="update-indexhtml"></a>A `index.html` frissítése

Most, hogy már van egy függvénye szöveg-beszéd átalakításra, és egy útvonal a lombik alkalmazásban való meghívásához, a következő lépés az alkalmazás HTML-címének megírása. Az alábbi HTML-fájl néhány dolgot tartalmaz:

* Hangválasztó legördülő menü
* Gomb hozzáadásával szöveggé alakítható át
* Egy hangelem hozzáadására szolgál, amely a szintetizált beszéd visszajátszására használatos

1. Nyissa meg `index.html` a következő kódot, és keresse meg a megjegyzéseket:
   ```html
   <!-- Start voice font selection code -->

   <!-- End voice font selection code -->
   ```

2. A kód megjegyzéseit cserélje le a következő HTML-blokkra:
   ```html
   <div class="form-group">
     <label for="select-voice"><strong>Select voice font:</strong></label>
     <select class="form-control" id="select-voice">
       <option value="(ar-SA, Naayf)">Arabic | Male | Naayf</option>
       <option value="(ca-ES, HerenaRUS)">Catalan | Female | HerenaRUS</option>
       <option value="(zh-CN, HuihuiRUS)">Chinese (Mainland) | Female | HuihuiRUS</option>
       <option value="(zh-CN, Kangkang, Apollo)">Chinese (Mainland) | Male | Kangkang, Apollo</option>
       <option value="(zh-HK, Tracy, Apollo)">Chinese (Hong Kong)| Female | Tracy, Apollo</option>
       <option value="(zh-HK, Danny, Apollo)">Chinese (Hong Kong) | Male | Danny, Apollo</option>
       <option value="(zh-TW, Yating, Apollo)">Chinese (Taiwan)| Female | Yating, Apollo</option>
       <option value="(zh-TW, Zhiwei, Apollo)">Chinese (Taiwan) | Male | Zhiwei, Apollo</option>
       <option value="(hr-HR, Matej)">Croatian | Male | Matej</option>
       <option value="(en-US, AriaRUS)">English (US) | Female | AriaRUS</option>
       <option value="(en-US, Guy24kRUS)">English (US) | Male | Guy24kRUS</option>
       <option value="(en-IE, Sean)">English (IE) | Male | Sean</option>
       <option value="(fr-FR, Julie, Apollo)">French | Female | Julie, Apollo</option>
       <option value="(fr-FR, HortenseRUS)">French | Female | Julie, HortenseRUS</option>
       <option value="(fr-FR, Paul, Apollo)">French | Male | Paul, Apollo</option>
       <option value="(de-DE, Hedda)">German | Female | Hedda</option>
       <option value="(de-DE, HeddaRUS)">German | Female | HeddaRUS</option>
       <option value="(de-DE, Stefan, Apollo)">German | Male | Apollo</option>
       <option value="(el-GR, Stefanos)">Greek | Male | Stefanos</option>
       <option value="(he-IL, Asaf)">Hebrew (Isreal) | Male | Asaf</option>
       <option value="(hi-IN, Kalpana, Apollo)">Hindi | Female | Kalpana, Apollo</option>
       <option value="(hi-IN, Hemant)">Hindi | Male | Hemant</option>
       <option value="(it-IT, LuciaRUS)">Italian | Female | LuciaRUS</option>
       <option value="(it-IT, Cosimo, Apollo)">Italian | Male | Cosimo, Apollo</option>
       <option value="(ja-JP, Ichiro, Apollo)">Japanese | Male | Ichiro</option>
       <option value="(ja-JP, HarukaRUS)">Japanese | Female | HarukaRUS</option>
       <option value="(ko-KR, HeamiRUS)">Korean | Female | Heami</option>
       <option value="(pt-BR, HeloisaRUS)">Portuguese (Brazil) | Female | HeloisaRUS</option>
       <option value="(pt-BR, Daniel, Apollo)">Portuguese (Brazil) | Male | Daniel, Apollo</option>
       <option value="(pt-PT, HeliaRUS)">Portuguese (Portugal) | Female | HeliaRUS</option>
       <option value="(ru-RU, Irina, Apollo)">Russian | Female | Irina, Apollo</option>
       <option value="(ru-RU, Pavel, Apollo)">Russian | Male | Pavel, Apollo</option>
       <option value="(ru-RU, EkaterinaRUS)">Russian | Female | EkaterinaRUS</option>
       <option value="(es-ES, Laura, Apollo)">Spanish | Female | Laura, Apollo</option>
       <option value="(es-ES, HelenaRUS)">Spanish | Female | HelenaRUS</option>
       <option value="(es-ES, Pablo, Apollo)">Spanish | Male | Pablo, Apollo</option>
       <option value="(th-TH, Pattara)">Thai | Male | Pattara</option>
       <option value="(tr-TR, SedaRUS)">Turkish | Female | SedaRUS</option>
       <option value="(vi-VN, An)">Vietnamese | Male | An</option>
     </select>
   </div>
   ```

3. Ezután keresse meg a következő kódokat:
   ```html
   <!-- Add Speech Synthesis button and audio element -->

   <!-- End Speech Synthesis button -->
   ```

4. A kód megjegyzéseit cserélje le a következő HTML-blokkra:

```html
<button type="submit" class="btn btn-primary mb-2" id="text-to-speech">Convert text-to-speech</button>
<div id="audio-playback">
  <audio id="audio" controls>
    <source id="audio-source" type="audio/mpeg" />
  </audio>
</div>
```

5. Ügyeljen arra, hogy mentse a munkáját.

### <a name="update-mainjs"></a>A `main.js` frissítése

Az alábbi kódban a HTML-ből származó tartalmat használjuk a lombik útvonalára vonatkozó kérelem létrehozásához. Pontosabban a fordítást és a hangbetűkészletet rendeli hozzá a rendszer a változóhoz, majd az útvonalra irányuló kérésben továbbítja azokat `text-to-speech` .

A kód ezután megismétli a választ, és frissíti a HTML-t az érzelmi pontszámokkal.

1. Az IDE-ből hozzon létre egy nevű fájlt `main.js` a `static` címtárban.
2. A kód másolása a következőbe `static/scripts/main.js` :
   ```javascript
   // Convert text-to-speech
   $("#text-to-speech").on("click", function(e) {
     e.preventDefault();
     var ttsInput = document.getElementById("translation-result").value;
     var ttsVoice = document.getElementById("select-voice").value;
     var ttsRequest = { 'text': ttsInput, 'voice': ttsVoice }

     var xhr = new XMLHttpRequest();
     xhr.open("post", "/text-to-speech", true);
     xhr.setRequestHeader("Content-Type", "application/json");
     xhr.responseType = "blob";
     xhr.onload = function(evt){
       if (xhr.status === 200) {
         audioBlob = new Blob([xhr.response], {type: "audio/mpeg"});
         audioURL = URL.createObjectURL(audioBlob);
         if (audioURL.length > 5){
           var audio = document.getElementById("audio");
           var source = document.getElementById("audio-source");
           source.src = audioURL;
           audio.load();
           audio.play();
         }else{
           console.log("An error occurred getting and playing the audio.")
         }
       }
     }
     xhr.send(JSON.stringify(ttsRequest));
   });
   // Code for automatic language selection goes here.
   ```
3. Már majdnem kész. Az utolsó teendő, hogy egy kód hozzáadásával `main.js` automatikusan kijelöl egy hangbetűkészletet a fordításhoz kiválasztott nyelv alapján. Kód hozzáadása a következőhöz `main.js` :
   ```javascript
   // Automatic voice font selection based on translation output.
   $('select[id="select-language"]').change(function(e) {
     if ($(this).val() == "ar"){
       document.getElementById("select-voice").value = "(ar-SA, Naayf)";
     }
     if ($(this).val() == "ca"){
       document.getElementById("select-voice").value = "(ca-ES, HerenaRUS)";
     }
     if ($(this).val() == "zh-Hans"){
       document.getElementById("select-voice").value = "(zh-HK, Tracy, Apollo)";
     }
     if ($(this).val() == "zh-Hant"){
       document.getElementById("select-voice").value = "(zh-HK, Tracy, Apollo)";
     }
     if ($(this).val() == "hr"){
       document.getElementById("select-voice").value = "(hr-HR, Matej)";
     }
     if ($(this).val() == "en"){
       document.getElementById("select-voice").value = "(en-US, Jessa24kRUS)";
     }
     if ($(this).val() == "fr"){
       document.getElementById("select-voice").value = "(fr-FR, HortenseRUS)";
     }
     if ($(this).val() == "de"){
       document.getElementById("select-voice").value = "(de-DE, HeddaRUS)";
     }
     if ($(this).val() == "el"){
       document.getElementById("select-voice").value = "(el-GR, Stefanos)";
     }
     if ($(this).val() == "he"){
       document.getElementById("select-voice").value = "(he-IL, Asaf)";
     }
     if ($(this).val() == "hi"){
       document.getElementById("select-voice").value = "(hi-IN, Kalpana, Apollo)";
     }
     if ($(this).val() == "it"){
       document.getElementById("select-voice").value = "(it-IT, LuciaRUS)";
     }
     if ($(this).val() == "ja"){
       document.getElementById("select-voice").value = "(ja-JP, HarukaRUS)";
     }
     if ($(this).val() == "ko"){
       document.getElementById("select-voice").value = "(ko-KR, HeamiRUS)";
     }
     if ($(this).val() == "pt"){
       document.getElementById("select-voice").value = "(pt-BR, HeloisaRUS)";
     }
     if ($(this).val() == "ru"){
       document.getElementById("select-voice").value = "(ru-RU, EkaterinaRUS)";
     }
     if ($(this).val() == "es"){
       document.getElementById("select-voice").value = "(es-ES, HelenaRUS)";
     }
     if ($(this).val() == "th"){
       document.getElementById("select-voice").value = "(th-TH, Pattara)";
     }
     if ($(this).val() == "tr"){
       document.getElementById("select-voice").value = "(tr-TR, SedaRUS)";
     }
     if ($(this).val() == "vi"){
       document.getElementById("select-voice").value = "(vi-VN, An)";
     }
   });
   ```

### <a name="test-your-app"></a>Az alkalmazás tesztelése

Tesztelje a beszédfelismerési szintézist az alkalmazásban.

```
flask run
```

Navigáljon a megadott kiszolgálócím-címre. Írja be a szövegbeviteli terület szövegét, válassza ki a nyelvet, majd nyomja le a fordítás gombot. Be kell szereznie egy fordítást. Ezután válasszon ki egy hangot, majd nyomja meg a szöveg konvertálása a beszédre gombot. a fordítást szintetizált beszédként kell lejátszani. Ha nem működik, győződjön meg arról, hogy az előfizetési kulcsot adta hozzá.

> [!TIP]
> Ha a végrehajtott módosítások nem jelennek meg, vagy az alkalmazás nem a várt módon működik, próbálja meg törölni a gyorsítótárat, vagy nyisson meg egy privát/inkognitóban ablakot.

Így van egy működő alkalmazása, amely elvégzi a fordításokat, elemzi a hangulatát és a szintetizált beszédet. Nyomja le a **CTRL + c** billentyűkombinációt az alkalmazás leöléséhez. Mindenképpen tekintse meg a többi [Azure-Cognitive Services](../index.yml).

## <a name="get-the-source-code"></a>A forráskód lekérése

A projekt forráskódja elérhető a [githubon](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial).

## <a name="next-steps"></a>Következő lépések

* [Fordítói dokumentáció](./reference/v3-0-reference.md)
* [Text Analytics API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)
* [Szöveg – beszéd API-hivatkozás](../speech-service/rest-text-to-speech.md)