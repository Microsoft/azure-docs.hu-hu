---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 10/20/2020
ms.author: trbye
ms.openlocfilehash: d2fae1c08132c855f570750a4528a9a37343434e
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105105335"
---
## <a name="install-the-speech-sdk"></a>A Speech SDK telepítése

Mielőtt bármit elvégezhet, telepítenie kell a <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">javascripthez készült SPEECH SDK </a>-t. A platformtól függően kövesse az alábbi utasításokat:

- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Webböngésző </a>

## <a name="create-voice-signatures"></a>Hangaláírások létrehozása

Első lépésként hozzon létre hangaláírásokat a beszélgetés résztvevőinek, hogy egyedi hangszórókként lehessen azonosítani őket. A `.wav` hangaláírások létrehozásához használt bemeneti hangfájlnak 16 bitesnek, 16 kHz-es mintavételi sebességnek és egy egycsatornás (monó) formátumúnak kell lennie. Az egyes hangminták javasolt hossza harminc másodperc és két perc között lehet. A `.wav` fájlnak egy **személy** hangjának kell lennie, hogy egyedi hangprofilt hozzon létre.

Az alábbi példa bemutatja, hogyan hozhat létre hangaláírást [a JavaScript-REST API használatával](https://aka.ms/cts/signaturegenservice) . Vegye figyelembe, hogy a `subscriptionKey` , a `region` és a minta fájl elérési útját kell helyettesítenie `.wav` .

```javascript
const fs = require('fs');
const axios = require('axios');
const formData = require('form-data');
 
const subscriptionKey = 'your-subscription-key';
const region = 'your-region';
 
async function createProfile() {
    let form = new formData();
    form.append('file', fs.createReadStream('path-to-voice-sample.wav'));
    let headers = form.getHeaders();
    headers['Ocp-Apim-Subscription-Key'] = subscriptionKey;
 
    let url = `https://signature.${region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData`;
    let response = await axios.post(url, form, { headers: headers });
    
    // get signature from response, serialize to json string
    return JSON.stringify(response.data.Signature);
}
 
async function main() {
    // use this voiceSignature string with conversation transcription calls below
    let voiceSignatureString = await createProfile();
    console.log(voiceSignatureString);
}
main();
```

A szkript futtatása a változóban a hangaláírási karakterláncot adja vissza `voiceSignatureString` . Futtassa kétszer a függvényt, hogy két karakterláncot használjon bemenetként a változóknál `voiceSignatureStringUser1` és az `voiceSignatureStringUser2` alábbiakban.

> [!NOTE]
> A hangaláírásokat **csak** a REST API használatával lehet létrehozni.

## <a name="transcribe-conversations"></a>Beszélgetések átirata

Az alábbi mintakód bemutatja, hogyan lehet valós időben átírni a beszélgetéseket két beszélő számára. Feltételezi, hogy már létrehozott egy hangaláírási karakterláncot az egyes hangszórókhoz a fent látható módon. Helyettesítse be a valódi információit a, a, a `subscriptionKey` `region` és az elérési utat az átírni `filepath` kívánt hanghoz.

Ez a mintakód a következő műveleteket végzi el:

* Létrehoz egy leküldéses adatfolyamot, amely az átíráshoz használható, és a mintát írja a `.wav` fájlba.
* Létrehoz egy-t a `Conversation` használatával `createConversationAsync()` .
* Létrehoz egy-t `ConversationTranscriber` a konstruktor használatával.
* Résztvevőket vesz fel a beszélgetésbe. A karakterláncokat `voiceSignatureStringUser1` , és `voiceSignatureStringUser2` kimenetként kell eljutni a fenti lépésekből.
* Regisztrálja az eseményeket, és megkezdi az átírást.

```javascript
(function() {
    "use strict";
    var sdk = require("microsoft-cognitiveservices-speech-sdk");
    var fs = require("fs");
    
    var subscriptionKey = "your-subscription-key";
    var region = "your-region";
    var filepath = "audio-file-to-transcribe.wav"; // 8-channel audio
    
    // create the push stream and write file to it
    var pushStream = sdk.AudioInputStream.createPushStream();
    fs.createReadStream(filepath).on('data', function(arrayBuffer) {
        pushStream.write(arrayBuffer.slice());
    }).on('end', function() {
        pushStream.close();
    });
    
    var speechTranslationConfig = sdk.SpeechTranslationConfig.fromSubscription(subscriptionKey, region);
    var audioConfig = sdk.AudioConfig.fromStreamInput(pushStream);
    speechTranslationConfig.speechRecognitionLanguage = "en-US";
    
    // create conversation and transcriber
    var conversation = sdk.Conversation.createConversationAsync(speechTranslationConfig, "myConversation");
    var transcriber = new sdk.ConversationTranscriber(audioConfig);
    
    // attach the transcriber to the conversation
    transcriber.joinConversationAsync(conversation,
    function () {
        // add first participant using voiceSignature created in enrollment step
        var user1 = sdk.Participant.From("user1@example.com", "en-us", voiceSignatureStringUser1);
        conversation.addParticipantAsync(user1,
        function () {
            // add second participant using voiceSignature created in enrollment step
            var user2 = sdk.Participant.From("user2@example.com", "en-us", voiceSignatureStringUser2);
            conversation.addParticipantAsync(user2,
            function () {
                transcriber.sessionStarted = function(s, e) {
                console.log("(sessionStarted)");
                };
                transcriber.sessionStopped = function(s, e) {
                console.log("(sessionStopped)");
                };
                transcriber.canceled = function(s, e) {
                console.log("(canceled)");
                };
                transcriber.transcribed = function(s, e) {
                console.log("(transcribed) text: " + e.result.text);
                console.log("(transcribed) speakerId: " + e.result.speakerId);
                };
    
                // begin conversation transcription
                transcriber.startTranscribingAsync(
                function () { },
                function (err) {
                    console.trace("err - starting transcription: " + err);
                });
        },
        function (err) {
            console.trace("err - adding user1: " + err);
        });
    },
    function (err) {
        console.trace("err - adding user2: " + err);
    });
    },
    function (err) {
    console.trace("err - " + err);
    });
}()); 
```