---
title: Lebilincselő olvasó Node.js ügyféloldali kódtár gyors üzembe helyezése
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban létrehozhat egy webalkalmazást a semmiből, és hozzáadhatja a magával ragadó olvasó API funkcióját.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 09/14/2020
ms.author: nitinme
ms.custom: devx-track-js
ms.openlocfilehash: ffef03457f9d3faf385bd0a924474dfa6efc4121
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102619240"
---
A teljes körűen kialakított [olvasó](https://www.onenote.com/learningtools) egy kipróbált eljárás, amely az új olvasók, a nyelvi tanulók és a tanulással kapcsolatos különbségeket, például a diszlexiat is javítja. Az alkalmazásokban magával ragadó olvasóval elkülönítheti a szöveget a fókusz növeléséhez, képeket jeleníthet meg a gyakran használt szavakhoz, kiemelheti a beszéd részeit, beolvashatja a kijelölt szöveget hangosan, lefordíthatja a szavakat és kifejezéseket valós időben, és így tovább.

Ebben a rövid útmutatóban egy webalkalmazást hoz létre a semmiből, és integrálhatja a magával ragadó olvasót az olvasó ügyféloldali kódtár használatával. Ennek a rövid útmutatónak a teljes működő mintája elérhető [a githubon](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/quickstart-nodejs).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services)
* A Azure Active Directory hitelesítéshez konfigurált, magával ragadó olvasó erőforrás. A beállításhoz kövesse az [alábbi utasításokat](../../how-to-create-immersive-reader.md) . A környezeti tulajdonságok konfigurálásakor itt létrehozott értékek némelyikére szüksége lesz. Mentse a munkamenet kimenetét szövegfájlba későbbi használatra.
* [Node.js](https://nodejs.org/) és [fonal](https://yarnpkg.com)
* IDE, például a [Visual Studio Code](https://code.visualstudio.com/)

## <a name="create-a-nodejs-web-app-with-express"></a>Node.js Webalkalmazás létrehozása Expressből

Hozzon létre egy Node.js webalkalmazást az `express-generator` eszközzel.

```bash
npm install express-generator -g
express --view=pug quickstart-nodejs
cd quickstart-nodejs
```

Telepítse a fonal-függőségeket, és adja hozzá a függőségeket, `request` `dotenv` amelyeket később a rövid útmutatóban fog használni.

```bash
yarn
yarn add request
yarn add dotenv
```

## <a name="set-up-authentication"></a>Hitelesítés beállítása

### <a name="configure-authentication-values"></a>Hitelesítési értékek konfigurálása

Hozzon létre egy _. env_ nevű új fájlt a projekt gyökerében. Illessze be a következő kódot a mezőbe, és adja meg azokat az értékeket, amelyeket a magával ragadó olvasó erőforrás létrehozásakor adott meg.
Ne tartalmazzon idézőjeleket vagy a "{" és "}" karaktereket.

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Ügyeljen arra, hogy ne véglegesítse ezt a fájlt a verziókövetés során, mert olyan titkos kulcsokat tartalmaz, amelyeket nem kell nyilvánosságra hozni.

Ezután nyissa meg _app.js_ , és adja hozzá a következőt a fájl elejéhez. Ezzel betölti a. env fájlban definiált tulajdonságokat környezeti változókként a csomópontba.

```javascript
require('dotenv').config();
```

### <a name="update-the-router-to-acquire-the-token"></a>Az útválasztó frissítése a jogkivonat beszerzéséhez
Nyissa meg a _routes\index.js_ fájlt, és cserélje le az automatikusan generált kódot a következő kódra.

Ez a kód egy olyan API-végpontot hoz létre, amely Azure AD-hitelesítési jogkivonatot szerzi be a szolgáltatás egyszerű jelszava használatával. Az altartományt is lekéri. Ezután visszaadja a jogkivonatot és altartományt tartalmazó objektumot.

```javascript
var express = require('express');
var router = express.Router();
var request = require('request');

/* GET home page. */
router.get('/', function(req, res, next) {
  res.render('index', { title: 'Express' });
});

router.get('/GetTokenAndSubdomain', function(req, res) {
    try {
        request.post({
            headers: {
                'content-type': 'application/x-www-form-urlencoded'
            },
            url: `https://login.windows.net/${process.env.TENANT_ID}/oauth2/token`,
            form: {
                grant_type: 'client_credentials',
                client_id: process.env.CLIENT_ID,
                client_secret: process.env.CLIENT_SECRET,
                resource: 'https://cognitiveservices.azure.com/'
            }
        },
        function(err, resp, tokenResult) {
            if (err) {
                console.log(err);
                return res.status(500).send('CogSvcs IssueToken error');
            }

            var tokenResultParsed = JSON.parse(tokenResult);

            if (tokenResultParsed.error) {
                console.log(tokenResult);
                return res.send({error :  "Unable to acquire Azure AD token. Check the debugger for more information."})
            }

            var token = tokenResultParsed.access_token;
            var subdomain = process.env.SUBDOMAIN;
            return res.send({token, subdomain});
        });
    } catch (err) {
        console.log(err);
        return res.status(500).send('CogSvcs IssueToken error');
    }
});

module.exports = router;
```

A **GetTokenAndSubdomain** API-végpontot biztonságossá kell tennie valamilyen hitelesítési módszer (például [OAuth](https://oauth.net/2/)) mögött, hogy megakadályozza a jogosulatlan felhasználók számára a jogkivonatok beszerzését a felhasználatlan olvasó szolgáltatás és a számlázás során. Ez a rövid útmutató hatókörén kívül esik.

## <a name="add-sample-content"></a>Minta tartalmának hozzáadása

Most hozzáadunk egy minta tartalmat ehhez a webalkalmazáshoz. Nyissa meg a _views\index.PUG_ , és cserélje le az automatikusan generált kódot a következő mintára:

```pug
doctype html
html
   head
      title Immersive Reader Quickstart Node.js

      link(rel='stylesheet', href='https://stackpath.bootstrapcdn.com/bootstrap/3.4.1/css/bootstrap.min.css')

      // A polyfill for Promise is needed for IE11 support.
      script(src='https://cdn.jsdelivr.net/npm/promise-polyfill@8/dist/polyfill.min.js')

      script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.1.0.0.js')
      script(src='https://code.jquery.com/jquery-3.3.1.min.js')

      style(type="text/css").
        .immersive-reader-button {
          background-color: white;
          margin-top: 5px;
          border: 1px solid black;
          float: right;
        }
   body
      div(class="container")
        button(class="immersive-reader-button" data-button-style="iconAndText" data-locale="en")

        h1(id="ir-title") About Immersive Reader
        div(id="ir-content" lang="en-us")
          p Immersive Reader is a tool that implements proven techniques to improve reading comprehension for emerging readers, language learners, and people with learning differences. The Immersive Reader is designed to make reading more accessible for everyone. The Immersive Reader

            ul
                li Shows content in a minimal reading view
                li Displays pictures of commonly used words
                li Highlights nouns, verbs, adjectives, and adverbs
                li Reads your content out loud to you
                li Translates your content into another language
                li Breaks down words into syllables

          h3 The Immersive Reader is available in many languages.

          p(lang="es-es") El Lector inmersivo está disponible en varios idiomas.
          p(lang="zh-cn") 沉浸式阅读器支持许多语言
          p(lang="de-de") Der plastische Reader ist in vielen Sprachen verfügbar.
          p(lang="ar-eg" dir="rtl" style="text-align:right") يتوفر \"القارئ الشامل\" في العديد من اللغات.

script(type="text/javascript").
  function getTokenAndSubdomainAsync() {
        return new Promise(function (resolve, reject) {
            $.ajax({
                url: "/GetTokenAndSubdomain",
                type: "GET",
                success: function (data) {
                    if (data.error) {
                        reject(data.error);
                    } else {
                        resolve(data);
                    }
                },
                error: function (err) {
                    reject(err);
                }
            });
        });
    }

    $(".immersive-reader-button").click(function () {
        handleLaunchImmersiveReader();
    });

    function handleLaunchImmersiveReader() {
        getTokenAndSubdomainAsync()
            .then(function (response) {
                const token = response["token"];
                const subdomain = response["subdomain"];
                // Learn more about chunk usage and supported MIME types https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference#chunk
                const data = {
                    title: $("#ir-title").text(),
                    chunks: [{
                        content: $("#ir-content").html(),
                        mimeType: "text/html"
                    }]
                };
                // Learn more about options https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference#options
                const options = {
                    "onExit": exitCallback,
                    "uiZIndex": 2000
                };
                ImmersiveReader.launchAsync(token, subdomain, data, options)
                    .catch(function (error) {
                        alert("Error in launching the Immersive Reader. Check the console.");
                        console.log(error);
                    });
            })
            .catch(function (error) {
                alert("Error in getting the Immersive Reader token and subdomain. Check the console.");
                console.log(error);
            });
    }

    function exitCallback() {
        console.log("This is the callback function. It is executed when the Immersive Reader closes.");
    }
```


Figyelje meg, hogy az összes szöveg **lang** attribútummal rendelkezik, amely a szöveg nyelveit írja le. Ez az attribútum segíti a magával ragadó olvasót a megfelelő nyelvi és nyelvtani funkciók biztosításában.

## <a name="build-and-run-the-app"></a>Készítsen buildet és futtassa az alkalmazást

A webalkalmazás most már készen áll. Indítsa el az alkalmazást a futtatásával:

```bash
npm start
```

Nyissa meg a böngészőt, és navigáljon a gombra _http://localhost:3000_ . A következőnek kell megjelennie:

![Minta alkalmazás – Node.js](../../media/quickstart-nodejs/1-buildapp.png)

## <a name="launch-the-immersive-reader"></a>A lebilincselő olvasó elindítása

Ha a "magára olvasó" gombra kattint, megjelenik a megjelenő, az oldalon található tartalommal ellátott olvasó.

![Lebilincselő olvasó – Node.js](../../media/quickstart-nodejs/2-viewimmersivereader.png)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Erőforrás létrehozása és HRE konfigurálása](../../how-to-create-immersive-reader.md)