---
title: Az SDK - beszédszolgáltatások beszédfelismerési hibaelhárítása
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
ms.openlocfilehash: 99cb23afcdb40f74485a7dcec34435a46d0e7476
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405905"
---
# <a name="troubleshoot-the-speech-sdk"></a>A Speech SDK hibaelhárítása

Ez a cikk a Speech SDK használata során felmerülő problémák megoldásához nyújt segítséget.

## <a name="error-websocket-upgrade-failed-with-an-authentication-error-403"></a>Hiba: A WebSocket frissítése hitelesítési hiba miatt nem sikerült (403)

Előfordulhat, hogy a nem megfelelő végpont az régió vagy szolgáltatás. Ellenőrizze, hogy helyes-e az URI-t.

Ezenkívül előfordulhat, a probléma az előfizetési kulcs vagy engedélyezési jogkivonat. További információkért tekintse meg a következő szakaszban.

## <a name="error-http-403-forbidden-or-http-401-unauthorized"></a>Hiba: HTTP 403 Tiltott vagy HTTP 401 jogosulatlan

Ez a hiba gyakran okozza hitelesítéssel kapcsolatos problémáiról. Csatlakozási kérelmek nem egy érvényes `Ocp-Apim-Subscription-Key` vagy `Authorization` fejléc a 403-as vagy a 401-es állapotot a rendszer elutasítja.

* Ha egy előfizetési kulcsot használ a hitelesítéshez, láthatja a hiba, mert:

    - az előfizetési kulcs nem található vagy érvénytelen
    - túllépte az előfizetés használati kvóta

* Ha egy engedélyezési jogkivonatot használ a hitelesítéshez, láthatja a hiba, mert:

    - a hitelesítési jogkivonat érvénytelen
    - a hitelesítési jogkivonat lejárt

### <a name="validate-your-subscription-key"></a>Az előfizetési kulcs ellenőrzése

Ellenőrizheti, hogy van-e egy érvényes előfizetési kulcsot a következő parancsok egyikét futtatja.

> [!NOTE]
> Cserélje le `YOUR_SUBSCRIPTION_KEY` és `YOUR_REGION` saját előfizetési kulcs és a társított régió.

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

### <a name="validate-an-authorization-token"></a>Egy engedélyezési jogkivonat érvényesítése

Egy engedélyezési jogkivonatot használnak a hitelesítéshez, ha annak ellenőrzéséhez, hogy az engedélyezési jogkivonatot továbbra is érvényes a következő parancsok egyikét futtatja. 10 percig jogkivonatok érvényesek.

> [!NOTE]
> Cserélje le `YOUR_AUDIO_FILE` a korábban rögzített hangfájl az elérési útját. Cserélje le `YOUR_ACCESS_TOKEN` az az engedélyezési jogkivonatot adott vissza az előző lépésben. Cserélje le `YOUR_REGION` és a megfelelő régió.

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

## <a name="error-http-400-bad-request"></a>Hiba: HTTP 400 – hibás kérelem

Ez a hiba általában akkor fordul elő, ha a kérés törzsében érvénytelen hang adatokat tartalmaz. Csak a WAV-formátum támogatott. Emellett ellenőrizze, hogy biztosan a megfelelő értékeket adja meg a kérelem fejlécek `Content-Type` és `Content-Length`.

## <a name="error-http-408-request-timeout"></a>Hiba: HTTP 408 kérelem időtúllépése

A hiba legvalószínűbb oka az, hogy nincs hang adatot küld a szolgáltatásnak. Ezt a hibát emellett okozhatja hálózati probléma.

## <a name="recognitionstatus-in-the-response-is-initialsilencetimeout"></a>A válasz a "RecognitionStatus" a "InitialSilenceTimeout"

A probléma okozza hívásaiból. Mivel ez a hiba jelenhetnek meg:

* Nincs hosszú többhelyes csend a hanganyag elején. Ebben az esetben a szolgáltatás leáll a felismerés néhány másodperc múlva, és adja vissza `InitialSilenceTimeout`.

* A hanganyag egy nem támogatott kodek formátumot, azaz a hang kell tekinteni a csend használja.

## <a name="next-steps"></a>További lépések

* [Tekintse át a kibocsátási megjegyzések](releasenotes.md)
