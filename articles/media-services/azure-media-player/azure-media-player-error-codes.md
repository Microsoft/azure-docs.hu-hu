---
title: Azure Media Player hibakódok
description: Hibakód-hivatkozás Azure Media Playerhoz.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: error-reference
ms.date: 04/20/2020
ms.openlocfilehash: 89f063ab50cfd880552e80a77ba798e4d06de5b3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101709580"
---
# <a name="error-codes"></a>Hibakódok #

Ha a lejátszás nem indítható el vagy leáll, a rendszer elindít egy hibaüzenetet, és a `error()` függvény egy kódot és egy opcionális üzenetet ad vissza, amely segítséget nyújt az alkalmazás fejlesztőnek további részletek megjelenítéséhez. `error().message` az üzenet nem jelenik meg a felhasználó számára.  A felhasználónak megjelenő üzenet a BITS 27-20-es verzióján alapul `error().code` , lásd az alábbi táblázatot.

```javascript

    var myPlayer = amp('vid1');
    myPlayer.addEventListener('error', function() {
        var errorDetails = myPlayer.error();
        var code = errorDetails.code;
        var message = errorDetails.message;
    }
```

## <a name="error-codes-bits-31-28-4-bits"></a>Hibakódok, BITS [31-28] (4 bit) ##

Írja le a hiba területét.

- 0 – ismeretlen
- 1 – AMP
- 2 – AzureHtml5JS
- 3 – vakuk
- 4 – Silverlight
- 5 – Html5
- 6 – Html5FairPlayHLS

## <a name="error-codes-bits-27-0-28-bits"></a>Hibakódok, BITS [27-0] (28 bit) ##

Írja le a hiba részleteit, a BITS 27-20 magas szintű, a BITS 19-0 további részleteket biztosít, ha elérhető.


| amp. errorCode. neve | Kódok, BITS [27-0] (28 bit) | Leírás |
|---|---:|---|
| **MEDIA_ERR_ABORTED hibák tartománya (0x0100000-0x01FFFFF)** | | |
| abortedErrUnknown | 0x0100000 | Általános megszakítási hiba |
| abortedErrNotImplemented | 0x0100001 | Megszakítási hiba, nincs implementálva |
| abortedErrHttpMixedContentBlocked | 0x0100002 | Megszakítási hiba, a kevert tartalom letiltva – általában akkor fordul elő, amikor egy `http://` adatfolyamot tölt be egy `https://` lapról |
| **MEDIA_ERR_NETWORK hibák indítási értéke (0x0200000-0x02FFFFF)** | | |
| networkErrUnknown | 0x0200000 | Általános hálózati hiba |
| networkErrHttpBadUrlFormat | 0x0200190 | Http 400-hiba válasza |
| networkErrHttpUserAuthRequired | 0x0200191 | HTTP 401-hiba válasza |
| networkErrHttpUserForbidden | 0x0200193 | Http 403-hiba válasza |
| networkErrHttpUrlNotFound | 0x0200194 | Http 404-hiba válasza |
| networkErrHttpNotAllowed | 0x0200195 | Http 405-hiba válasza |
| networkErrHttpGone | 0x020019A | Http 410-hiba válasza |
| networkErrHttpPreconditionFailed | 0x020019C | Http 412-hiba válasza |
| networkErrHttpInternalServerFailure | 0x02001F4 | Http 500-hiba válasza
| networkErrHttpBadGateway | 0x02001F6 | Http 502-hiba válasza |
| networkErrHttpServiceUnavailable | 0x02001F7 | Http 503-hiba válasza |
| networkErrHttpGatewayTimeout | 0x02001F8 | Http 504-hiba válasza |
| networkErrTimeout | 0x0200258 | Hálózati időtúllépési hiba
| networkErrErr | 0x0200259 | Hálózati kapcsolatok hibájának válasza |
| **MEDIA_ERR_DECODE hibák (0x0300000-0x03FFFFF)** | | |
| decodeErrUnknown | 0x0300000 | Általános dekódolási hiba |
| **MEDIA_ERR_SRC_NOT_SUPPORTED hibák (0x0400000-0x04FFFFF)** | | |
| srcErrUnknown | 0x0400000 | Általános forrás nem támogatott hiba |
| srcErrParsePresentation | 0x0400001 | Bemutató-elemzési hiba |
| srcErrParseSegment | 0x0400002 | Szegmens elemzési hibája |
| srcErrUnsupportedPresentation | 0x0400003 | A bemutató nem támogatott |
| srcErrInvalidSegment | 0x0400004 | Érvénytelen szegmens |
| **MEDIA_ERR_ENCRYPTED hibák indítási értéke (0x0500000-0x05FFFFF)** | | |
| encryptErrUnknown | 0x0500000 | Általános titkosított hiba | 
| encryptErrDecrypterNotFound | 0x0500001 | Az Decrypter nem található |
| encryptErrDecrypterInit | 0x0500002 | Decrypter Inicializálási hiba |
| encryptErrDecrypterNotSupported | 0x0500003 | A Decrypter nem támogatott |
| encryptErrKeyAcquire | 0x0500004 | A kulcs beolvasása nem sikerült |
| encryptErrDecryption | 0x0500005 | A szegmens visszafejtése nem sikerült |
| encryptErrLicenseAcquire | 0x0500006 | A licenc megszerzése nem sikerült |
| **SRC_PLAYER_MISMATCH hibák indítási értéke (0x0600000-0x06FFFFF)** | | |
| srcPlayerMismatchUnknown | 0x0600000 | Általános nem megfelelő Tech Player a forrás lejátszásához |
| srcPlayerMismatchFlashNotInstalled | 0x0600001 |A Flash beépülő modul nincs telepítve, ha telepítve van a forrás. *Vagy* A Flash 30 telepítve van, és az AES-tartalmat játssza le.  Ha ez a helyzet, próbálkozzon egy másik böngészővel. A Flash 30 a mai napig nem támogatott a június 7-én. További részletekért tekintse meg az [ismert problémák](azure-media-player-known-issues.md) című témakört. Megjegyzés: Ha a 0x00600003, a Flash és a Silverlight is nincs telepítve, ha meg van adva a techOrder.|
| srcPlayerMismatchSilverlightNotInstalled | 0x0600002 | A Silverlight beépülő modul nincs telepítve, ha telepítve van a forrás. Megjegyzés: Ha a 0x00600003, a Flash és a Silverlight is nincs telepítve, ha meg van adva a techOrder. |
| | 0x00600003 | A Flash és a Silverlight nincs telepítve, ha meg van adva a techOrder. |
| **Ismeretlen hibák (0x0FF00000)** | | |
| errUnknown | 0xFF00000 | Ismeretlen hibák |

## <a name="user-error-messages"></a>Felhasználói hibaüzenetek ##

A megjelenített felhasználói üzenet a hibakód BITS 27-20-es értékén alapul.

- MEDIA_ERR_ABORTED (1) – "megszakította a videó lejátszását"
- MEDIA_ERR_NETWORK (2) – "A hálózati hiba miatt a videó letöltése sikertelen volt."
- MEDIA_ERR_DECODE (3) – "a videolejátszás egy sérülési probléma miatt megszakadt, vagy a videó által használt funkciók a böngészőben nem támogatottak."
- MEDIA_ERR_SRC_NOT_SUPPORTED (4) – "a videó nem tölthető be, mert a kiszolgáló vagy a hálózat nem sikerült, vagy mert a formátum nem támogatott."
- MEDIA_ERR_ENCRYPTED (5) – "a videó titkosítva van, és nem tudjuk visszafejteni a kulcsokat."
- SRC_PLAYER_MISMATCH (6) – "nem található kompatibilis forrás ehhez a videóhoz."
- MEDIA_ERR_UNKNOWN (0xFF) – "Ismeretlen hiba történt."

## <a name="examples"></a>Példák ##

### <a name="0x10600001"></a>0x10600001 ##

"Nem található kompatibilis forrás ehhez a videóhoz." megjelenik a végfelhasználó számára.

Nincs olyan Tech Player, amely képes lejátszani a kért forrásokat, de ha a Flash beépülő modul telepítve van, akkor valószínű, hogy a forrás játszható.  

### <a name="0x20200194"></a>0x20200194 ###

"A hálózati hiba miatt a videó letöltése sikertelen volt." megjelenik a végfelhasználó számára.

A AzureHtml5JS nem tudott lejátszani egy HTTP 404-válaszból.

### <a name="categorizing-errors"></a>Hibák kategorizálása ###

```javascript
    if(myPlayer.error().code & amp.errorCode.abortedErrStart) {
        // MEDIA_ERR_ABORTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.networkErrStart) {
        // MEDIA_ERR_NETWORK errors
    }
    else if(myPlayer.error().code & amp.errorCode.decodeErrStart) {
        // MEDIA_ERR_DECODE errors
    }
    else if(myPlayer.error().code & amp.errorCode.srcErrStart) {
        // MEDIA_ERR_SRC_NOT_SUPPORTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.encryptErrStart) {
        // MEDIA_ERR_ENCRYPTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.srcPlayerMismatchStart) {
        // SRC_PLAYER_MISMATCH errors
    }
    else {
        // unknown errors
    }
```

### <a name="catching-a-specific-error"></a>Egy adott hiba kifogása ###

A következő kód mindössze 404 hibát észlel:

```javascript
    if(myPlayer.error().code & amp.errorCode.networkErrHttpUrlNotFound) {
        // all http 404 errors
    }
```

## <a name="next-steps"></a>Következő lépések ##

- [Azure Media Player rövid útmutató](azure-media-player-quickstart.md)