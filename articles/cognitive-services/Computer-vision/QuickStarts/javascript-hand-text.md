---
title: 'Gyors útmutató: Computer Vision 2,0 és 2,1 – nyomtatott és kézzel írott szöveg kinyerése – REST, JavaScript'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban Kinyeri a nyomtatott és a kézírásos szöveget a Computer Vision API és a JavaScript használatával.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 35988f10703967bd5986015ccb0fb480679b94e9
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81404750"
---
# <a name="quickstart-extract-printed-and-handwritten-text-using-the-computer-vision-rest-api-and-javascript"></a>Gyors útmutató: nyomtatott és kézírásos szöveg kinyerése a Computer Vision REST API és a JavaScript használatával

Ebben a rövid útmutatóban Kinyeri a nyomtatott és/vagy kézzel írott szöveget a Computer Vision REST API használatával. A [Batch olvasási](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) és [olvasási műveletének eredményének](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/5be108e7498a4f9ed20bf96d) módszereivel a képeken lévő szövegeket azonosíthatja, és kinyerheti a felismert karaktereket egy géppel olvasható karakteres adatfolyamba. Az API meghatározza, hogy melyik felismerési modellt kell használni az egyes szövegekhez, így a nyomtatott és a kézírásos szöveggel is támogatja a lemezképeket.

Ez a funkció a v 2.1 API-ban és a v 3.0 nyilvános előzetes verziójú API-ban is elérhető. A 2.1-es verzióhoz képest a 3,0 API a következő:

* Javított pontosság
* A szavak megbízhatósági pontszámai
* A spanyol és az angol nyelv támogatása a további `language` paraméterrel
* Egy másik kimeneti formátum

Válassza ki az alábbi lapot az Ön által használt verzióhoz.

#### <a name="version-2"></a>[2-es verzió](#tab/version-2)

> [!IMPORTANT]
> A [Batch olvasási](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) metódusa aszinkron módon fut. Ez a metódus nem adja vissza információt a sikeres válaszok törzsében. Ehelyett a Batch olvasási metódus egy URI-t ad vissza a `Operation-Location` válasz fejléc mezőjének értékeként. Ezt követően meghívhatja ezt az URI-t, amely az [olvasási művelet eredménye](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/5be108e7498a4f9ed20bf96d) API-t jelöli, és a kötegelt olvasási metódus hívásának eredményeit is visszaadja.

#### <a name="version-3-public-preview"></a>[3. verzió (nyilvános előzetes verzió)](#tab/version-3)

> [!IMPORTANT]
> A [Batch olvasási](https://westus2.dev.cognitive.microsoft.com/docs/services/5d98695995feb7853f67d6a6/operations/5d986960601faab4bf452005) metódusa aszinkron módon fut. Ez a metódus nem adja vissza információt a sikeres válaszok törzsében. Ehelyett a Batch olvasási metódus egy URI-t ad vissza a `Operation-Location` válasz fejléc mezőjének értékeként. Ezt követően meghívhatja ezt az URI-t, amely az [olvasási művelet eredménye](https://westus2.dev.cognitive.microsoft.com/docs/services/5d98695995feb7853f67d6a6/operations/5d9869604be85dee480c8750) API-t jelöli, és a kötegelt olvasási metódus hívásának eredményeit is visszaadja.

---

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) .

Szüksége lesz egy Computer Vision-előfizetői azonosítóra. A [kipróbálási Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision)ingyenes próbaverziós kulcsot is beszerezhet. Vagy kövesse a [Cognitive Services fiók létrehozása](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) az Computer Visionra való előfizetéshez és a kulcs beszerzéséhez című témakör utasításait. Mentse az előfizetési kulcsot és a végpont URL-címét egy ideiglenes helyre.

## <a name="create-and-run-the-sample"></a>A minta létrehozása és futtatása

#### <a name="version-2"></a>[2-es verzió](#tab/version-2)

A minta létrehozásához és futtatásához az alábbi lépéseket kell végrehajtania:

1. Hozzon létre egy _Get-Text. html_nevű fájlt, nyissa meg egy szövegszerkesztőben, és másolja a következő kódot.
1. Szükség esetén cserélje le a `value` `inputImage` vezérlő attribútumának értékét egy másik olyan rendszerkép URL-címére, amelyből szöveget szeretne kinyerni.
1. Nyisson meg egy böngészőablakot.
1. A böngészőben húzza a fájlt a böngészőablakba.
1. Ha a weblap megjelenik a böngészőben, illessze be az előfizetési kulcsot és a végponti URL-címet a megfelelő beviteli mezőkbe.
1. Válassza a **rendszerkép olvasása** gombot.

```html
<!DOCTYPE html>
<html>
<head>
    <title>Text Recognition Sample</title>
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.9.0/jquery.min.js"></script>
</head>
<body>

<script type="text/javascript">
    function processImage() {
        // **********************************************
        // *** Update or verify the following values. ***
        // **********************************************

        var subscriptionKey = document.getElementById("subscriptionKey").value;
        var endpoint = document.getElementById("endpointUrl").value;
        
        var uriBase = endpoint + "vision/v2.1/read/core/asyncBatchAnalyze";

        // Display the image.
        var sourceImageUrl = document.getElementById("inputImage").value;
        document.querySelector("#sourceImage").src = sourceImageUrl;

        // This operation requires two REST API calls. One to submit the image
        // for processing, the other to retrieve the text found in the image.
        //
        // Make the first REST API call to submit the image for processing.
        $.ajax({
            url: uriBase,

            // Request headers.
            beforeSend: function(jqXHR){
                jqXHR.setRequestHeader("Content-Type","application/json");
                jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            },

            type: "POST",

            // Request body.
            data: '{"url": ' + '"' + sourceImageUrl + '"}',
        })

        .done(function(data, textStatus, jqXHR) {
            // Show progress.
            $("#responseTextArea").val("Text submitted. " +
                "Waiting 10 seconds to retrieve the recognized text.");

            // Note: The response may not be immediately available. Text
            // recognition is an asynchronous operation that can take a variable
            // amount of time depending on the length of the text you want to
            // recognize. You may need to wait or retry the GET operation.
            //
            // Wait ten seconds before making the second REST API call.
            setTimeout(function () {
                // "Operation-Location" in the response contains the URI
                // to retrieve the recognized text.
                var operationLocation = jqXHR.getResponseHeader("Operation-Location");

                // Make the second REST API call and get the response.
                $.ajax({
                    url: operationLocation,

                    // Request headers.
                    beforeSend: function(jqXHR){
                        jqXHR.setRequestHeader("Content-Type","application/json");
                        jqXHR.setRequestHeader(
                            "Ocp-Apim-Subscription-Key", subscriptionKey);
                    },

                    type: "GET",
                })

                .done(function(data) {
                    // Show formatted JSON on webpage.
                    $("#responseTextArea").val(JSON.stringify(data, null, 2));
                })

                .fail(function(jqXHR, textStatus, errorThrown) {
                    // Display error message.
                    var errorString = (errorThrown === "") ? "Error. " :
                        errorThrown + " (" + jqXHR.status + "): ";
                    errorString += (jqXHR.responseText === "") ? "" :
                        (jQuery.parseJSON(jqXHR.responseText).message) ?
                            jQuery.parseJSON(jqXHR.responseText).message :
                            jQuery.parseJSON(jqXHR.responseText).error.message;
                    alert(errorString);
                });
            }, 10000);
        })

        .fail(function(jqXHR, textStatus, errorThrown) {
            // Put the JSON description into the text area.
            $("#responseTextArea").val(JSON.stringify(jqXHR, null, 2));

            // Display error message.
            var errorString = (errorThrown === "") ? "Error. " :
                errorThrown + " (" + jqXHR.status + "): ";
            errorString += (jqXHR.responseText === "") ? "" :
                (jQuery.parseJSON(jqXHR.responseText).message) ?
                    jQuery.parseJSON(jqXHR.responseText).message :
                    jQuery.parseJSON(jqXHR.responseText).error.message;
            alert(errorString);
        });
    };
</script>
<h1>Read text from image:</h1>
Enter the URL to an image of text, then click
the <strong>Read image</strong> button.
<br><br>
Subscription key: 
<input type="text" name="subscriptionKey" id="subscriptionKey"
    value="" /> 
Endpoint URL:
<input type="text" name="endpointUrl" id="endpointUrl"
    value="" />
<br><br>
Image to read:
<input type="text" name="inputImage" id="inputImage"
    value="https://upload.wikimedia.org/wikipedia/commons/thumb/d/dd/Cursive_Writing_on_Notebook_paper.jpg/800px-Cursive_Writing_on_Notebook_paper.jpg" />
<button onclick="processImage()">Read image</button>
<br><br>
<div id="wrapper" style="width:1020px; display:table;">
    <div id="jsonOutput" style="width:600px; display:table-cell;">
        Response:
        <br><br>
        <textarea id="responseTextArea" class="UIInput"
                  style="width:580px; height:400px;"></textarea>
    </div>
    <div id="imageDiv" style="width:420px; display:table-cell;">
        Source image:
        <br><br>
        <img id="sourceImage" width="400" />
    </div>
</div>
</body>
</html>
```

#### <a name="version-3-public-preview"></a>[3. verzió (nyilvános előzetes verzió)](#tab/version-3)

A minta létrehozásához és futtatásához az alábbi lépéseket kell végrehajtania:

1. Másolja az alábbi kódot egy szövegszerkesztőbe.
1. Szükség esetén cserélje le a `value` `inputImage` vezérlő attribútumának értékét egy másik olyan rendszerkép URL-címére, amelyből szöveget szeretne kinyerni.
1. Mentse a kódot egy `.html` kiterjesztésű fájlként. Például: `get-text.html`.
1. Nyisson meg egy böngészőablakot.
1. Ha a weblap megjelenik a böngészőben, töltse ki a szükséges paramétereket, és válassza a **rendszerkép olvasása** gombot.

```html
<!DOCTYPE html>
<html>
<head>
    <title>Text Recognition Sample</title>
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.9.0/jquery.min.js"></script>
</head>
<body>

<script type="text/javascript">
    function processImage() {
        // **********************************************
        // *** Update or verify the following values. ***
        // **********************************************

        let subscriptionKey = document.getElementById("key").value;
        let endpoint = document.getElementById("endpoint").value;
        if (!subscriptionKey) { throw new Error('Set your environment variables for your subscription key and endpoint.'); }
        let language = document.getElementById("language").value;
        
        var uriBase = endpoint + "/vision/v3.0-preview/read/analyze";

        // Display the image.
        var sourceImageUrl = document.getElementById("inputImage").value;
        document.querySelector("#sourceImage").src = sourceImageUrl;

        const params = {
            language: language,
        };

        const searchParams = new URLSearchParams(params);

        // This operation requires two REST API calls. One to submit the image
        // for processing, the other to retrieve the text found in the image.
        //
        // Make the first REST API call to submit the image for processing.
        $.ajax({
            url: uriBase + "?" + searchParams.toString(),

            // Request headers.
            beforeSend: function(jqXHR){
                jqXHR.setRequestHeader("Content-Type","application/json");
                jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            },

            type: "POST",

            // Request body.
            data: JSON.stringify({url: sourceImageUrl}),
        })

        .done(function(data, textStatus, jqXHR) {
            // Show progress.
            $("#responseTextArea").val("Text submitted. " +
                "Waiting 10 seconds to retrieve the recognized text.");

            // Note: The response may not be immediately available. Text
            // recognition is an asynchronous operation that can take a variable
            // amount of time depending on the length of the text you want to
            // recognize. You may need to wait or retry the GET operation.
            //
            // Wait ten seconds before making the second REST API call.
            setTimeout(function () {
                // "Operation-Location" in the response contains the URI
                // to retrieve the recognized text.
                var operationLocation = jqXHR.getResponseHeader("Operation-Location");

                // Make the second REST API call and get the response.
                $.ajax({
                    url: operationLocation,

                    // Request headers.
                    beforeSend: function(jqXHR){
                        jqXHR.setRequestHeader("Content-Type","application/json");
                        jqXHR.setRequestHeader(
                            "Ocp-Apim-Subscription-Key", subscriptionKey);
                    },

                    type: "GET",
                })

                .done(function(data) {
                    // Show formatted JSON on webpage.
                    $("#responseTextArea").val(JSON.stringify(data, null, 2));
                })

                .fail(function(jqXHR, textStatus, errorThrown) {
                    // Display error message.
                    var errorString = (errorThrown === "") ? "Error. " :
                        errorThrown + " (" + jqXHR.status + "): ";
                    errorString += (jqXHR.responseText === "") ? "" :
                        (jQuery.parseJSON(jqXHR.responseText).message) ?
                            jQuery.parseJSON(jqXHR.responseText).message :
                            jQuery.parseJSON(jqXHR.responseText).error.message;
                    alert(errorString);
                });
            }, 10000);
        })

        .fail(function(jqXHR, textStatus, errorThrown) {
            // Put the JSON description into the text area.
            $("#responseTextArea").val(JSON.stringify(jqXHR, null, 2));

            // Display error message.
            var errorString = (errorThrown === "") ? "Error. " :
                errorThrown + " (" + jqXHR.status + "): ";
            errorString += (jqXHR.responseText === "") ? "" :
                (jQuery.parseJSON(jqXHR.responseText).message) ?
                    jQuery.parseJSON(jqXHR.responseText).message :
                    jQuery.parseJSON(jqXHR.responseText).error.message;
            alert(errorString);
        });
    };
</script>
<h1>Read text from image:</h1>
Enter the URL to an image of text, then click
the <strong>Read image</strong> button.
<br><br>
Endpoint: 
<input type="text" name="endpoint" id="endpoint" value="" style="width: 300px;"/>
<div style="margin: 20px;">Example: https://westus2.api.cognitive.microsoft.com</div>
Subscription Key:    
<input type="text" name="key" id="key" value="" style="width: 300px;"/>
<br><br>
Language: 
<input type="text" name="language" id="language"
    value="en" />
<div style="margin: 20px;">Accepted values are &quot;en&quot; and &quot;es&quot;</div>

<br><br>
Image to read:
<input type="text" name="inputImage" id="inputImage"
    value="https://upload.wikimedia.org/wikipedia/commons/thumb/d/dd/Cursive_Writing_on_Notebook_paper.jpg/800px-Cursive_Writing_on_Notebook_paper.jpg" />
<button onclick="processImage()">Read image</button>
<br><br>
<div id="wrapper" style="width:1020px; display:table;">
    <div id="jsonOutput" style="width:600px; display:table-cell;">
        Response:
        <br><br>
        <textarea id="responseTextArea" class="UIInput"
                  style="width:580px; height:400px;"></textarea>
    </div>
    <div id="imageDiv" style="width:420px; display:table-cell;">
        Source image:
        <br><br>
        <img id="sourceImage" width="400" />
    </div>
</div>
</body>
</html>
```

---

## <a name="examine-the-response"></a>A válasz vizsgálata

A rendszer JSON formátumban adja vissza a sikeres választ. A mintaweblap elemzi és megjeleníti a sikeres választ a böngészőablakban, a következő példához hasonló módon:

#### <a name="version-2"></a>[2-es verzió](#tab/version-2)

```json
{
  "status": "Succeeded",
  "recognitionResults": [
    {
      "page": 1,
      "clockwiseOrientation": 349.59,
      "width": 3200,
      "height": 3200,
      "unit": "pixel",
      "lines": [
        {
          "boundingBox": [202,618,2047,643,2046,840,200,813],
          "text": "Our greatest glory is not",
          "words": [
            {
              "boundingBox": [204,627,481,628,481,830,204,829],
              "text": "Our"
            },
            {
              "boundingBox": [519,628,1057,630,1057,832,518,830],
              "text": "greatest"
            },
            {
              "boundingBox": [1114,630,1549,631,1548,833,1114,832],
              "text": "glory"
            },
            {
              "boundingBox": [1586,631,1785,632,1784,834,1586,833],
              "text": "is"
            },
            {
              "boundingBox": [1822,632,2115,633,2115,835,1822,834],
              "text": "not"
            }
          ]
        },
        {
          "boundingBox": [420,1273,2954,1250,2958,1488,422,1511],
          "text": "but in rising every time we fall",
          "words": [
            {
              "boundingBox": [423,1269,634,1268,635,1507,424,1508],
              "text": "but"
            },
            {
              "boundingBox": [667,1268,808,1268,809,1506,668,1507],
              "text": "in"
            },
            {
              "boundingBox": [874,1267,1289,1265,1290,1504,875,1506],
              "text": "rising"
            },
            {
              "boundingBox": [1331,1265,1771,1263,1772,1502,1332,1504],
              "text": "every"
            },
            {
              "boundingBox": [1812, 1263, 2178, 1261, 2179, 1500, 1813, 1502],
              "text": "time"
            },
            {
              "boundingBox": [2219, 1261, 2510, 1260, 2511, 1498, 2220, 1500],
              "text": "we"
            },
            {
              "boundingBox": [2551, 1260, 3016, 1258, 3017, 1496, 2552, 1498],
              "text": "fall"
            }
          ]
        },
        {
          "boundingBox": [1612, 903, 2744, 935, 2738, 1139, 1607, 1107],
          "text": "in never failing ,",
          "words": [
            {
              "boundingBox": [1611, 934, 1707, 933, 1708, 1147, 1613, 1147],
              "text": "in"
            },
            {
              "boundingBox": [1753, 933, 2132, 930, 2133, 1144, 1754, 1146],
              "text": "never"
            },
            {
              "boundingBox": [2162, 930, 2673, 927, 2674, 1140, 2164, 1144],
              "text": "failing"
            },
            {
              "boundingBox": [2703, 926, 2788, 926, 2790, 1139, 2705, 1140],
              "text": ",",
              "confidence": "Low"
            }
          ]
        }
      ]
    }
  ]
}
```

#### <a name="version-3-public-preview"></a>[3. verzió (nyilvános előzetes verzió)](#tab/version-3)

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-02-11T20:56:33Z",
  "lastUpdatedDateTime": "2020-02-11T20:56:34Z",
  "analyzeResult": {
    "version": "3.0.0",
    "readResults": [
      {
        "page": 1,
        "language": "en",
        "angle": 0.8206,
        "width": 800,
        "height": 154,
        "unit": "pixel",
        "lines": [
          {
            "language": "en",
            "boundingBox": [
              6,
              4,
              774,
              14,
              773,
              61,
              5,
              49
            ],
            "text": "The quick brown fox jumps over the lazy",
            "words": [
              {
                "boundingBox": [
                  14,
                  5,
                  76,
                  6,
                  74,
                  49,
                  12,
                  48
                ],
                "text": "The",
                "confidence": 0.83
              },
              {
                "boundingBox": [
                  84,
                  6,
                  182,
                  7,
                  180,
                  51,
                  82,
                  49
                ],
                "text": "quick",
                "confidence": 0.762
              },
              {
                "boundingBox": [
                  191,
                  7,
                  312,
                  9,
                  309,
                  54,
                  189,
                  51
                ],
                "text": "brown",
                "confidence": 0.67
              },
              {
                "boundingBox": [
                  320,
                  9,
                  382,
                  10,
                  379,
                  55,
                  317,
                  54
                ],
                "text": "fox",
                "confidence": 0.849
              },
              {
                "boundingBox": [
                  390,
                  10,
                  497,
                  11,
                  493,
                  57,
                  387,
                  55
                ],
                "text": "jumps",
                "confidence": 0.703
              },
              {
                "boundingBox": [
                  506,
                  11,
                  596,
                  12,
                  591,
                  59,
                  502,
                  57
                ],
                "text": "over",
                "confidence": 0.799
              },
              {
                "boundingBox": [
                  604,
                  12,
                  666,
                  13,
                  661,
                  60,
                  600,
                  59
                ],
                "text": "the",
                "confidence": 0.923
              },
              {
                "boundingBox": [
                  674,
                  13,
                  773,
                  14,
                  768,
                  62,
                  670,
                  60
                ],
                "text": "lazy",
                "confidence": 0.863
              }
            ]
          },
          {
            "language": "en",
            "boundingBox": [
              5,
              53,
              79,
              56,
              77,
              95,
              4,
              92
            ],
            "text": "dog",
            "words": [
              {
                "boundingBox": [
                  6,
                  53,
                  74,
                  56,
                  72,
                  95,
                  5,
                  92
                ],
                "text": "dog",
                "confidence": 0.418
              }
            ]
          },
          {
            "language": "en",
            "boundingBox": [
              0,
              90,
              787,
              95,
              787,
              145,
              0,
              136
            ],
            "text": "Pack my box with five dozen liquor jugs",
            "words": [
              {
                "boundingBox": [
                  1,
                  96,
                  79,
                  93,
                  79,
                  135,
                  0,
                  136
                ],
                "text": "Pack",
                "confidence": 0.835
              },
              {
                "boundingBox": [
                  87,
                  93,
                  151,
                  92,
                  151,
                  135,
                  87,
                  135
                ],
                "text": "my",
                "confidence": 0.88
              },
              {
                "boundingBox": [
                  162,
                  92,
                  226,
                  91,
                  225,
                  135,
                  161,
                  135
                ],
                "text": "box",
                "confidence": 0.301
              },
              {
                "boundingBox": [
                  234,
                  91,
                  335,
                  90,
                  335,
                  135,
                  233,
                  135
                ],
                "text": "with",
                "confidence": 0.959
              },
              {
                "boundingBox": [
                  346,
                  91,
                  418,
                  91,
                  417,
                  136,
                  345,
                  135
                ],
                "text": "five",
                "confidence": 0.489
              },
              {
                "boundingBox": [
                  426,
                  91,
                  527,
                  93,
                  527,
                  138,
                  425,
                  136
                ],
                "text": "dozen",
                "confidence": 0.727
              },
              {
                "boundingBox": [
                  554,
                  94,
                  687,
                  98,
                  687,
                  143,
                  553,
                  139
                ],
                "text": "liquor",
                "confidence": 0.377
              },
              {
                "boundingBox": [
                  701,
                  99,
                  787,
                  103,
                  787,
                  146,
                  700,
                  143
                ],
                "text": "jugs",
                "confidence": 0.693
              }
            ]
          }
        ]
      }
    ]
  }
}
```

---

## <a name="next-steps"></a>További lépések

Az optikai karakterfelismerés (OCR) elvégzéséhez Computer Visiont használó JavaScript-alkalmazás megismerése intelligens vágású miniatűrök létrehozása; a képek vizuális funkcióinak felismerése, kategorizálása, címkézése és leírása. 

> [!div class="nextstepaction"]
> [Computer Vision API – JavaScript-oktatóanyag](../Tutorials/javascript-tutorial.md)

* A Computer Vision API-val való gyors kísérletezéshez próbálja ki az [Open API-tesztkonzolt](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).