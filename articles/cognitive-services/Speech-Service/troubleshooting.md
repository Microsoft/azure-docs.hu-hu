---
title: A Speech SDK – Speech szolgáltatás hibáinak megoldása
titleSuffix: Azure Cognitive Services
description: Ez a cikk a Speech SDK használata során felmerülő problémák megoldásához nyújt segítséget.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: jhakulin
ms.openlocfilehash: 421b9adf4ae5d2c641484e646bea096716d46cca
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "74815409"
---
# <a name="troubleshoot-the-speech-sdk"></a>A Speech SDK hibaelhárítása

Ez a cikk a Speech SDK használata során felmerülő problémák megoldásához nyújt segítséget.

## <a name="error-websocket-upgrade-failed-with-an-authentication-error-403"></a>Hiba: a WebSocket frissítése hitelesítési hiba miatt nem sikerült (403)

Lehet, hogy a régiója vagy a szolgáltatása nem megfelelő végpontot tartalmaz. Ellenőrizze az URI-t, és győződjön meg arról, hogy helyes.

Előfordulhat, hogy az előfizetési kulcs vagy az engedélyezési jogkivonat problémába ütközik. További információt a következő szakaszban talál.

## <a name="error-http-403-forbidden-or-http-401-unauthorized"></a>Hiba: HTTP 403 Tiltott vagy HTTP 401 jogosulatlan

Ezt a hibát gyakran a hitelesítési problémák okozzák. Az érvényes `Ocp-Apim-Subscription-Key` vagy fejléc nélküli kapcsolatkérelmek `Authorization` 403 vagy 401 állapotú.

* Ha előfizetés-kulcsot használ a hitelesítéshez, a következő hibaüzenet jelenhet meg:

    - Az előfizetési kulcs hiányzik vagy érvénytelen
    - Túllépte az előfizetés használati kvótáját

* Ha hitelesítési tokent használ a hitelesítéshez, a következő hibaüzenet jelenhet meg:

    - Az engedélyezési jogkivonat érvénytelen
    - Az engedélyezési jogkivonat lejárt

### <a name="validate-your-subscription-key"></a>Előfizetési kulcs ellenőrzése

A következő parancsok egyikének futtatásával ellenőrizheti, hogy rendelkezik-e érvényes előfizetési kulccsal.

> [!NOTE]
> Cserélje le `YOUR_SUBSCRIPTION_KEY` a és a értékét `YOUR_REGION` a saját előfizetési kulcsára és a hozzá tartozó régióra.

* PowerShell

    ```Powershell
    $FetchTokenHeader = @{
      'Content-type'='application/x-www-form-urlencoded'
      'Content-Length'= '0'
      'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
    }
    $OAuthToken = Invoke-RestMethod -Method POST -Uri https://YOUR_REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken -Headers $FetchTokenHeader
    $OAuthToken
    ```

* cURL

    ```
    curl -v -X POST "https://YOUR_REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0"
    ```

Ha érvényes előfizetési kulcsot adott meg, a parancs egy engedélyezési jogkivonatot ad vissza, ellenkező esetben a rendszer hibát jelez.

### <a name="validate-an-authorization-token"></a>Engedélyezési jogkivonat ellenőrzése

Ha hitelesítési tokent használ a hitelesítéshez, futtassa a következő parancsok egyikét annak ellenőrzéséhez, hogy az engedélyezési jogkivonat továbbra is érvényes. A tokenek 10 percig érvényesek.

> [!NOTE]
> Cserélje le `YOUR_AUDIO_FILE` az értékét az előre rögzített hangfájl elérési útjára. Cserélje le az `YOUR_ACCESS_TOKEN` elemet az előző lépésben visszaadott engedélyezési jogkivonatra. Cserélje le `YOUR_REGION` a helyére a megfelelő régiót.

* PowerShell

    ```Powershell
    $SpeechServiceURI =
    'https://YOUR_REGION.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US'

    # $OAuthToken is the authorization token returned by the token service.
    $RecoRequestHeader = @{
      'Authorization' = 'Bearer '+ $OAuthToken
      'Transfer-Encoding' = 'chunked'
      'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
    }

    # Read audio into byte array.
    $audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

    $RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

    # Show the result.
    $RecoResponse
    ```

* cURL

    ```
    curl -v -X POST "https://YOUR_REGION.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Transfer-Encoding: chunked" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
    ```

Ha érvényes engedélyezési jogkivonatot adott meg, a parancs visszaadja a hangfájl átírását, ellenkező esetben a rendszer hibaüzenetet ad vissza.

---

## <a name="error-http-400-bad-request"></a>Hiba: hibás HTTP 400-kérelem

Ez a hiba általában akkor fordul elő, ha a kérelem törzse érvénytelen hangadatokat tartalmaz. Csak a WAV formátum támogatott. Továbbá ellenőrizze a kérelem fejléceit, és győződjön meg arról, hogy a és a megfelelő értékeket adta meg `Content-Type` `Content-Length` .

## <a name="error-http-408-request-timeout"></a>Hiba: HTTP 408 kérelem időtúllépése

A hiba valószínűleg azért fordul elő, mert a szolgáltatás nem küld hangadatokat. Ezt a hibát a hálózati problémák is okozhatják.

## <a name="recognitionstatus-in-the-response-is-initialsilencetimeout"></a>A válasz "RecognitionStatus" értéke "InitialSilenceTimeout"

Ezt a problémát általában a hangadatok okozzák. Ez a hiba a következő okból jelenhet meg:

* A hang elején hosszú a csend. Ebben az esetben a szolgáltatás néhány másodperc elteltével leállítja az elismerést, és visszaadja `InitialSilenceTimeout` .

* A hang nem támogatott codec formátumot használ, ami miatt a hangadatok csendként lesznek kezelve.

## <a name="next-steps"></a>Következő lépések

* [A kibocsátási megjegyzések áttekintése](releasenotes.md)
