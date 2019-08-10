---
title: Rendszerkép-műveletek végrehajtása – JavaScript
titleSuffix: Azure Cognitive Services
description: Ismerkedés egy alapszintű JavaScript-alkalmazással, amely a Computer Vision API-t használja az Azure Cognitive Services szolgáltatásban. OCR végrehajtása, miniatűrök létrehozása és képek vizuális jellemzőinek használata.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: tutorial
ms.date: 04/30/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: df2aef9a6401d0a3a1807fb7e869f03f9b7bcd36
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881237"
---
# <a name="use-computer-vision-features-with-the-rest-api-and-javascript"></a>Computer Vision szolgáltatások használata a REST API és a JavaScript használatával

Ez az útmutató az Azure Cognitive Services Computer Vision REST API funkcióit mutatja be.

Ismerjen meg egy JavaScript-alkalmazást, amely a Computer Vision REST API segítségével végez optikai karakterfelismerést (OCR), és amellyel intelligens körbevágású miniatűröket hozhat létre, valamint képek vizuális jellemzőit, például arcokat észlelhet, kategorizálhat, címkézhet és írhat le. Ebben a példában megadhatja egy kép URL-címét elemzés vagy feldolgozás céljából. Ez a nyílt forráskódú példa sablonként használható saját, a Computer Vision REST API használatához készült JavaScript-alkalmazások fejlesztéséhez.

A JavaScript-űrlapalkalmazást már létrehoztuk, azonban még működésképtelen. Ebben az útmutatóban az alkalmazás működésének befejezéséhez adja hozzá a Computer Vision REST APIhoz tartozó kódot.

## <a name="prerequisites"></a>Előfeltételek

### <a name="platform-requirements"></a>Platformkövetelmények

Az útmutató lépéseit egy egyszerű szövegszerkesztővel követheti el.

### <a name="subscribe-to-computer-vision-api-and-get-a-subscription-key"></a>Előfizetés a Computer Vision API-ra és egy előfizetési kulcs beszerzése

A példa létrehozása előtt elő kell fizetnie a Computer Vision API-ra, amely az Azure Cognitive Services része. Az előfizetésről és a kulcskezelésről további információt az [Előfizetések](https://azure.microsoft.com/try/cognitive-services/) című témakörben találhat. Az elsődleges és a másodlagos kulcsok is érvényesek az útmutatóban való használatra.

## <a name="acquire-incomplete-tutorial-project"></a>Hiányos oktatóanyag-projekt beolvasása

### <a name="download-the-project"></a>A projekt letöltése

Klónozza a [Cognitive Services JavaScript Computer Vision oktatóanyagot](https://github.com/Azure-Samples/cognitive-services-javascript-computer-vision-tutorial), vagy töltse le a .zip-fájlt, és csomagolja ki egy üres könyvtárban.

Ha az összes hozzáadott oktatóanyag-kóddal szeretné használni a befejezett projektet, használhatja a **Befejezve** mappában található fájlokat.

## <a name="add-tutorial-code-to-the-project"></a>Oktatóanyag-kód hozzáadása a projekthez

A JavaScript-alkalmazás hat .html-fájlt tartalmaz, függvényenként egyet. Minden fájl Computer Vision (elemzés, OCR stb.) eltérő funkcióját mutatja be. A hat szakaszban nem találhatók egymásrautaltságok, így hozzáadhatja az oktatóanyag kódját egy fájlhoz, mind a hat fájlhoz, vagy csak néhány fájlhoz. Ezt bármilyen sorrendben megteheti.

### <a name="analyze-an-image"></a>Rendszerkép elemzése

A Computer Vision elemzése funkció több ezer felismerhető objektum, az élőlények, a díszletek és a műveletek képét vizsgálja. Az elemzés után az Elemzés egy JSON-objektumot hoz létre, amely leíró címkékkel, színelemzéssel, feliratokkal és egyebekkel ismerteti a képet.

Az alkalmazás elemzés funkciójának befejezéséhez hajtsa végre a következő lépéseket:

#### <a name="add-the-event-handler-code-for-the-analyze-button"></a>Az elemzés gomb eseménykezelő kódjának hozzáadása

Nyissa meg az **analyze.html** fájlt egy szövegszerkesztőben, és keresse meg az **analyzeButtonClick** függvényt a fájl alján.

Az **analyzeButtonClick** eseménykezelő függvény törli az űrlapot, megjeleníti az URL-címben megadott képet, majd meghívja az **AnalyzeImage** függvényt a kép elemzéséhez. Illessze be az alábbi kódot az **analyzeButtonClick** függvénybe.

```javascript
function analyzeButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    AnalyzeImage(sourceImageUrl, $("#responseTextArea"), $("#captionSpan"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>A REST API-hívás burkolójának hozzáadása

Az **AnalyzeImage** függvény burkolja a REST API-hívást a kép elemzéséhez. Sikeres visszatérés esetén a formázott JSON-elemzés a megszabott szövegterületen jelenik meg, a felirat pedig a megadott keretben.

Illessze be az **AnalyzeImage** függvényt az **analyzeButtonClick** függvény alá.

```javascript
/* Analyze the image at the specified URL by using Microsoft Cognitive Services Analyze Image API.
 * @param {string} sourceImageUrl - The URL to the image to analyze.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 * @param {<span> element} captionSpan - The span to display the image caption.
 */
function AnalyzeImage(sourceImageUrl, responseTextArea, captionSpan) {
    // Request parameters.
    var params = {
        "visualFeatures": "Categories,Description,Color",
        "details": "",
        "language": "en",
    };
    
    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseAnalyze +
             "?" + 
             $.param(params),
                    
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
        
        // Extract and display the caption and confidence from the first caption in the description object.
        if (data.description && data.description.captions) {
            var caption = data.description.captions[0];
            
            if (caption.text && caption.confidence) {
                captionSpan.text("Caption: " + caption.text +
                    " (confidence: " + caption.confidence + ").");
            }
        }
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Prepare the error string.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        
        // Put the error JSON in the response textarea.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Show the error message.
        alert(errorString);
    });
}
```

#### <a name="run-the-analyze-function"></a>Az elemzés funkció futtatása

Mentse az **analyze.html** fájlt, és nyissa meg egy webböngészőben. Helyezze el az előfizetési kulcsot az **Előfizetési kulcs** mezőben, majd ellenőrizze, hogy a megfelelő régió van-e beállítva az **Előfizetési régió** mezőben. Adja meg egy elemzendő kép URL-címét, majd kattintson a **Kép elemzése** gombra a kép elemzéséhez és az eredmény megjelenítéséhez.

### <a name="recognize-a-landmark"></a>Nevezetesség felismerése

A Computer Vision Nevezetesség funkciója természetes és mesterséges nevezetességeket (például hegyeket vagy híres épületeket) elemez a képeken. Az elemzés befejezése után a Nevezetesség egy JSON-objektumot eredményez, amely a képen található nevezetességeket azonosítja.

Az alkalmazás tereptárgy funkciójának végrehajtásához hajtsa végre a következő lépéseket:

#### <a name="add-the-event-handler-code-for-the-landmark-button"></a>Az eseménykezelő kódjának hozzáadása a mérföldkő gombhoz

Nyissa meg a **landmark.html** fájlt egy szövegszerkesztőben, és keresse meg a **landmarkButtonClick** függvényt a fájl alján.

A **landmarkButtonClick** eseménykezelő függvény törli az űrlapot, megjeleníti az URL-címben megadott képet, majd meghívja az **IdentifyLandmarks** függvényt a kép elemzéséhez. Illessze be az alábbi kódot a **landmarkButtonClick** függvénybe.

```javascript
function landmarkButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    IdentifyLandmarks(sourceImageUrl, $("#responseTextArea"), $("#captionSpan"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>A REST API-hívás burkolójának hozzáadása

Az **IdentifyLandmarks** függvény burkolja a REST API-hívást a kép elemzéséhez. Sikeres visszatérés esetén a formázott JSON-elemzés a megszabott szövegterületen jelenik meg, a felirat pedig a megadott keretben.

Másolja és be az **IdentifyLandmarks** függvénykódot a **landmarkButtonClick** függvény alá.

```javascript
/* Identify landmarks in the image at the specified URL by using Microsoft Cognitive Services 
 * Landmarks API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for landmarks.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 * @param {<span> element} captionSpan - The span to display the image caption.
 */
function IdentifyLandmarks(sourceImageUrl, responseTextArea, captionSpan) {
    // Request parameters.
    var params = {
        "model": "landmarks"
    };
    
    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseLandmark +
             "?" + 
             $.param(params),
                    
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
        
        // Extract and display the caption and confidence from the first caption in the description object.
        if (data.result && data.result.landmarks) {
            var landmark = data.result.landmarks[0];
            
            if (landmark.name && landmark.confidence) {
                captionSpan.text("Landmark: " + landmark.name +
                    " (confidence: " + landmark.confidence + ").");
            }
        }
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Prepare the error string.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        
        // Put the error JSON in the response textarea.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Show the error message.
        alert(errorString);
    });
}
```

#### <a name="run-the-landmark-function"></a>A Landmark függvény futtatása

Mentse a **landmark.html** fájlt, és nyissa meg egy webböngészőben. Helyezze el az előfizetési kulcsot az **Előfizetési kulcs** mezőben, majd ellenőrizze, hogy a megfelelő régió van-e beállítva az **Előfizetési régió** mezőben. Adja meg egy elemzendő kép URL-címét, majd kattintson a **Kép elemzése** gombra a kép elemzéséhez és az eredmény megjelenítéséhez.

### <a name="recognize-celebrities"></a>Hírességek felismerése

A Computer Vision Hírességek funkciója hírességek alapján elemez egy képet. Az elemzés befejezése után a Hírességek egy JSON-objektumot eredményez, amely a képen található hírességeket azonosítja.

Az alkalmazás hírességek funkciójának végrehajtásához hajtsa végre a következő lépéseket:

#### <a name="add-the-event-handler-code-for-the-celebrities-button"></a>Adja hozzá az eseménykezelő kódját a hírességek gombhoz

Nyissa meg a **celebrities.html** fájlt egy szövegszerkesztőben, és keresse meg a **celebritiesButtonClick** függvényt a fájl alján.

A **celebritiesButtonClick** eseménykezelő függvény törli az űrlapot, megjeleníti az URL-címben megadott képet, majd meghívja az **IdentifyCelebrities** függvényt a kép elemzéséhez. Illessze be az alábbi kódot a **celebritiesButtonClick** függvénybe.

```javascript
function celebritiesButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    IdentifyCelebrities(sourceImageUrl, $("#responseTextArea"), $("#captionSpan"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>A REST API-hívás burkolójának hozzáadása

```javascript
/* Identify celebrities in the image at the specified URL by using Microsoft Cognitive Services 
 * Celebrities API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for celebrities.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 * @param {<span> element} captionSpan - The span to display the image caption.
 */
function IdentifyCelebrities(sourceImageUrl, responseTextArea, captionSpan) {
    // Request parameters.
    var params = {
        "model": "celebrities"
    };
    
    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseCelebrities +
             "?" + 
             $.param(params),
                    
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
        
        // Extract and display the caption and confidence from the first caption in the description object.
        if (data.result && data.result.celebrities) {
            var celebrity = data.result.celebrities[0];
            
            if (celebrity.name && celebrity.confidence) {
                captionSpan.text("Celebrity name: " + celebrity.name +
                    " (confidence: " + celebrity.confidence + ").");
            }
        }
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Prepare the error string.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        
        // Put the error JSON in the response textarea.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Show the error message.
        alert(errorString);
    });
}
```

#### <a name="run-the-celebrities-function"></a>A hírességek funkció futtatása

Mentse a **celebrities.html** fájlt, és nyissa meg egy webböngészőben. Helyezze el az előfizetési kulcsot az **Előfizetési kulcs** mezőben, majd ellenőrizze, hogy a megfelelő régió van-e beállítva az **Előfizetési régió** mezőben. Adja meg egy elemzendő kép URL-címét, majd kattintson a **Kép elemzése** gombra a kép elemzéséhez és az eredmény megjelenítéséhez.

### <a name="intelligently-generate-a-thumbnail"></a>Miniatűr intelligens létrehozása

A Computer Vision Miniatűr funkciója miniatűrt készít egy képből. Az **intelligens körbevágás** funkció segítségével a Miniatűr funkció azonosítja egy kép lényeges területét, majd ez alapján egy miniatűrt hoz létre, így mindig esztétikusabb miniatűröket készít.

Az alkalmazás miniatűr funkciójának végrehajtásához hajtsa végre a következő lépéseket:

#### <a name="add-the-event-handler-code-for-the-thumbnail-button"></a>Adja hozzá az eseménykezelő kódját a miniatűr gombhoz

Nyissa meg a **thumbnail.html** fájlt egy szövegszerkesztőben, és keresse meg a **thumbnailButtonClick** függvényt a fájl alján.

A **thumbnailButtonClick** eseménykezelő függvény törli az űrlapot, megjeleníti az URL-címben megadott képet, majd meghívja kétszer a **getThumbnail** függvényt két miniatűr létrehozásához; egy intelligensen körülvágott és egy körülvágás nélküli képhez. Illessze be az alábbi kódot a **thumbnailButtonClick** függvénybe.

```javascript
function thumbnailButtonClick() {

    // Clear the display fields.
    document.getElementById("sourceImage").src = "#";
    document.getElementById("thumbnailImageSmartCrop").src = "#";
    document.getElementById("thumbnailImageNonSmartCrop").src = "#";
    document.getElementById("responseTextArea").value = "";
    document.getElementById("captionSpan").text = "";
    
    // Display the image.
    var sourceImageUrl = document.getElementById("inputImage").value;
    document.getElementById("sourceImage").src = sourceImageUrl;
    
    // Get a smart cropped thumbnail.
    getThumbnail (sourceImageUrl, true, document.getElementById("thumbnailImageSmartCrop"), 
        document.getElementById("responseTextArea"));
    
    // Get a non-smart-cropped thumbnail.
    getThumbnail (sourceImageUrl, false, document.getElementById("thumbnailImageNonSmartCrop"),
        document.getElementById("responseTextArea"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>A REST API-hívás burkolójának hozzáadása

A **getThumbnail** függvény burkolja a REST API-hívást a kép elemzéséhez. Sikeres visszatérés esetén a miniatűr a megadott képelemben jelenik meg.

Illessze be a **getThumbnail** függvényt a **thumbnailButtonClick** függvény alá.

```javascript
/* Get a thumbnail of the image at the specified URL by using Microsoft Cognitive Services
 * Thumbnail API.
 * @param {string} sourceImageUrl URL to image.
 * @param {boolean} smartCropping Set to true to use the smart cropping feature which crops to the
 *                                more interesting area of an image; false to crop for the center
 *                                of the image.
 * @param {<img> element} imageElement The img element in the DOM which will display the thumbnail image.
 * @param {<textarea> element} responseTextArea - The text area to display the Response Headers returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 */
function getThumbnail (sourceImageUrl, smartCropping, imageElement, responseTextArea) {
    // Create the HTTP Request object.
    var xhr = new XMLHttpRequest();
    
    // Request parameters.
    var params = "width=100&height=150&smartCropping=" + smartCropping.toString();

    // Build the full URI.
    var fullUri = common.uriBasePreRegion + 
                  document.getElementById("subscriptionRegionSelect").value + 
                  common.uriBasePostRegion + 
                  common.uriBaseThumbnail +
                  "?" + 
                  params;
    
    // Identify the request as a POST, with the URI and parameters.
    xhr.open("POST", fullUri);
    
    // Add the request headers.
    xhr.setRequestHeader("Content-Type","application/json");
    xhr.setRequestHeader("Ocp-Apim-Subscription-Key", 
        encodeURIComponent(document.getElementById("subscriptionKeyInput").value));
    
    // Set the response type to "blob" for the thumbnail image data.
    xhr.responseType = "blob";
    
    // Process the result of the REST API call.
    xhr.onreadystatechange = function(e) {
        if(xhr.readyState === XMLHttpRequest.DONE) {
            
            // Thumbnail successfully created.
            if (xhr.status === 200) {
                // Show response headers.
                var s = JSON.stringify(xhr.getAllResponseHeaders(), null, 2);
                responseTextArea.value = JSON.stringify(xhr.getAllResponseHeaders(), null, 2);
                
                // Show thumbnail image.
                var urlCreator = window.URL || window.webkitURL;
                var imageUrl = urlCreator.createObjectURL(this.response);
                imageElement.src = imageUrl;
            } else {
                // Display the error message. The error message is the response body as a JSON string. 
                // The code in this code block extracts the JSON string from the blob response.
                var reader = new FileReader();
                
                // This event fires after the blob has been read.
                reader.addEventListener('loadend', (e) => {
                    responseTextArea.value = JSON.stringify(JSON.parse(e.srcElement.result), null, 2);
                });
                
                // Start reading the blob as text.
                reader.readAsText(xhr.response);
            }
        }
    }
    
    // Execute the REST API call.
    xhr.send('{"url": ' + '"' + sourceImageUrl + '"}');
}
```

#### <a name="run-the-thumbnail-function"></a>A miniatűr függvény futtatása

Mentse a **thumbnail.html** fájlt, és nyissa meg egy webböngészőben. Helyezze el az előfizetési kulcsot az **Előfizetési kulcs** mezőben, majd ellenőrizze, hogy a megfelelő régió van-e beállítva az **Előfizetési régió** mezőben. Adja meg egy elemzendő kép URL-címét, majd kattintson a **Miniatűrök létrehozása** gombra a kép elemzéséhez és az eredmény megtekintéséhez.

### <a name="read-printed-text-ocr"></a>Nyomtatott szöveg felolvasása (OCR)

A Computer Vision optikai karakterfelismerés (OCR) funkciója nyomtatott szöveget tartalmazó képeket elemez. Az elemzés befejezése után az OCR egy JSON-objektumot eredményez, amely a szöveget és annak a képen elfoglalt helyét tartalmazza.

Az alkalmazás OCR funkciójának végrehajtásához hajtsa végre a következő lépéseket:

### <a name="add-the-event-handler-code-for-the-ocr-button"></a>Adja hozzá az eseménykezelő kódját az OCR gombhoz

Nyissa meg az **ocr.html** fájlt egy szövegszerkesztőben, és keresse meg az **ocrButtonClick** függvényt a fájl alján.

Az **ocrButtonClick** eseménykezelő függvény törli az űrlapot, megjeleníti az URL-címben megadott képet, majd meghívja az **ReadOcrImage** függvényt a kép elemzéséhez. Illessze be az alábbi kódot az **ocrButtonClick** függvénybe.

```javascript
function ocrButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    ReadOcrImage(sourceImageUrl, $("#responseTextArea"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>A REST API-hívás burkolójának hozzáadása

A **ReadOcrImage** függvény burkolja a REST API-hívást egy kép elemzéséhez. Sikeres visszatérés után megjelenik egy formázott JSON, amely ismertei a szöveget, annak helye pedig a megadott szövegterületen jelenik meg.

Illessze be a **ReadOcrImage** függvényt az **ocrButtonClick** függvény alá.

```javascript
/* Recognize and read printed text in an image at the specified URL by using Microsoft Cognitive 
 * Services OCR API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for printed text.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 */
function ReadOcrImage(sourceImageUrl, responseTextArea) {
    // Request parameters.
    var params = {
        "language": "unk",
        "detectOrientation ": "true",
    };

    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseOcr +
             "?" + 
             $.param(params),
        
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Put the JSON description into the text area.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Display error message.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        alert(errorString);
    });
}
```

#### <a name="run-the-ocr-function"></a>Az OCR-függvény futtatása

Mentse az **ocr.html** fájlt, és nyissa meg egy webböngészőben. Helyezze el az előfizetési kulcsot az **Előfizetési kulcs** mezőben, majd ellenőrizze, hogy a megfelelő régió van-e beállítva az **Előfizetési régió** mezőben. Aadja meg egy olvasandó szöveget tartalmazó kép URL-címét, majd kattintson a **Kép felolvasása** gombra a kép elemzéséhez és az eredmény megjelenítéséhez.

### <a name="read-handwritten-text-handwriting-recognition"></a>Kézzel írt szöveg felolvasása (kézírás-felismerés)

A Computer Vision kézírás-felismerés funkciója kézzel írt szöveget tartalmazó képeket elemez. Az elemzés befejezése a kézírás-felismerés egy JSON-objektumot eredményez, amely a szöveget és annak a képen elfoglalt helyét tartalmazza.

Az alkalmazás kézírás-felismerési funkciójának végrehajtásához hajtsa végre a következő lépéseket:

#### <a name="add-the-event-handler-code-for-the-handwriting-button"></a>Az eseménykezelő kódjának hozzáadása a kézírás gombhoz

Nyissa meg a **handwriting.html** fájlt egy szövegszerkesztőben, és keresse meg az **handwritingButtonClick** függvényt a fájl alján.

A **handwritingButtonClick** eseménykezelő függvény törli az űrlapot, megjeleníti az URL-címben megadott képet, majd meghívja a **HandwritingImage** függvényt a kép elemzéséhez.

Illessze be az alábbi kódot a **handwritingButtonClick** függvénybe.

```javascript
function handwritingButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    ReadHandwrittenImage(sourceImageUrl, $("#responseTextArea"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>A REST API-hívás burkolójának hozzáadása

A **ReadHandwrittenImage** függvény burkolja a kép elemzéséhez szükséges két REST API-hívást. Mivel a kézírás-felismerés egy időigényes folyamat, kétlépéses megoldást alkalmaz. Az első hívás beküldi a képet feldolgozásra, míg a második lekéri az észlelt szöveget a feldolgozás után.

Miután lekérte a szöveget, megjelenik egy formázott JSON, amely ismertei a szöveget, annak helye pedig a megadott szövegterületen jelenik meg.

Illessze be a **ReadHandwrittenImage** függvényt a **handwritingButtonClick** függvény alá.

```javascript
/* Recognize and read text from an image of handwriting at the specified URL by using Microsoft 
 * Cognitive Services Recognize Handwritten Text API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for handwriting.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 */
function ReadHandwrittenImage(sourceImageUrl, responseTextArea) {
    // Request parameters.
    var params = {
        "handwriting": "true",
    };

    // This operation requires two REST API calls. One to submit the image for processing,
    // the other to retrieve the text found in the image. 
    //
    // Perform the first REST API call to submit the image for processing.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseHandwriting +
             "?" + 
             $.param(params),
        
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data, textStatus, jqXHR) {
        // Show progress.
        responseTextArea.val("Handwritten image submitted.");
        
        // Note: The response may not be immediately available. Handwriting Recognition is an
        // async operation that can take a variable amount of time depending on the length
        // of the text you want to recognize. You may need to wait or retry this GET operation.
        //
        // Try once per second for up to ten seconds to receive the result.
        var tries = 10;
        var waitTime = 100;
        var taskCompleted = false;
        
        var timeoutID = setInterval(function () { 
            // Limit the number of calls.
            if (--tries <= 0) {
                window.clearTimeout(timeoutID);
                responseTextArea.val("The response was not available in the time allowed.");
                return;
            }

            // The "Operation-Location" in the response contains the URI to retrieve the recognized text.
            var operationLocation = jqXHR.getResponseHeader("Operation-Location");
            
            // Perform the second REST API call and get the response.
            $.ajax({
                url: operationLocation,
                
                // Request headers.
                beforeSend: function(jqXHR){
                    jqXHR.setRequestHeader("Content-Type","application/json");
                    jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key",
                        encodeURIComponent($("#subscriptionKeyInput").val()));
                },
                
                type: "GET",
            })
            
            .done(function(data) {
                // If the result is not yet available, return.
                if (data.status && (data.status === "NotStarted" || data.status === "Running")) {
                    return;
                }
                
                // Show formatted JSON on webpage.
                responseTextArea.val(JSON.stringify(data, null, 2));
                
                // Indicate the task is complete and clear the timer.
                taskCompleted = true;
                window.clearTimeout(timeoutID);
            })
            
            .fail(function(jqXHR, textStatus, errorThrown) {
                // Indicate the task is complete and clear the timer.
                taskCompleted = true;
                window.clearTimeout(timeoutID);
                
                // Display error message.
                var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
                errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
                    jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
                alert(errorString);
            });
        }, waitTime);
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Put the JSON description into the text area.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Display error message.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        alert(errorString);
    });
}
```

#### <a name="run-the-handwriting-function"></a>A kézírás függvény futtatása

Mentse a **handwriting.html** fájlt, és nyissa meg egy webböngészőben. Helyezze el az előfizetési kulcsot az **Előfizetési kulcs** mezőben, majd ellenőrizze, hogy a megfelelő régió van-e beállítva az **Előfizetési régió** mezőben. Aadja meg egy olvasandó szöveget tartalmazó kép URL-címét, majd kattintson a **Kép felolvasása** gombra a kép elemzéséhez és az eredmény megjelenítéséhez.

## <a name="next-steps"></a>További lépések

Ebben az útmutatóban a Computer Vision REST APIt használta a JavaScript használatával a rendelkezésre álló képelemzési funkciók számos funkciójának teszteléséhez. Következő lépésként tekintse meg a dokumentációt, ahol többet tudhat meg az API-król.

- [Computer Vision REST API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)
