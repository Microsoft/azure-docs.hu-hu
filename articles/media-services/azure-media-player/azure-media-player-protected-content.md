---
title: Azure Media Player védett tartalom
description: A Azure Media Player jelenleg támogatja az AES-128 bites boríték titkosított tartalmát és a közösen titkosított tartalmakat.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.custom: devx-track-js
ms.openlocfilehash: 83f144c06c23f3ab5507e3561be4a12350e20a42
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91329686"
---
# <a name="protected-content"></a>Védett tartalom #

A Azure Media Player jelenleg támogatja az AES-128 bites borítékok titkosított tartalmait és közös titkosított tartalmait (PlayReady és Widevine) vagy titkosított tartalmat a FairPlay-en keresztül. A védett tartalom megfelelő lejátszásához meg kell adnia Azure Media Player a következőt: `protectionInfo` . Ez az információ forrásként szerepel, és közvetlenül a `<source>` címkén keresztül vehető fel a címkébe `data-setup` .  `protectionInfo`Ha dinamikusan állítja be a forrást, a közvetlenül paramétert is hozzáadhatja.

`protectionInfo` elfogad egy JSON-objektumot, és a következőket tartalmazza:

- `type`: `AES` vagy `PlayReady` vagy `Widevine``FairPlay`
- `certificateUrl`: az üzemeltetett FairPlay tanúsítvány közvetlen hivatkozásának kell lennie

- `authenticationToken`: ez egy választható mező, amely nem kódolt hitelesítési tokent ad hozzá

> [!IMPORTANT]
> A **certificateUrl** objektumra csak a Fairplay DRM esetében van szükség. * * *
>[!NOTE]
> Az alapértelmezett techOrder úgy módosult, hogy megfeleljen az új technológiának – `html5FairPlayHLS` kifejezetten a Fairplay-tartalmak natív módon történő lejátszásához az azt támogató böngészőkön (Safari on OSX 8 +). Ha FairPlay tartalommal rendelkezik, **és** módosította az alapértelmezett techOrder egy egyénire az alkalmazásban, akkor ezt az új technológiát hozzá kell adnia a techOrder objektumhoz. Azt javasoljuk, hogy a Silverlight előtt vegye fel azt, hogy a tartalom ne legyen lejátszás a PlayReady-n keresztül.

## <a name="code-sample"></a>Kódminta ##

```html
Ex:

    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source
            src="//example/path/to/myVideo.ism/manifest"
            type="application/vnd.ms-sstr+xml"
            data-setup='{"protectionInfo": [{"type": "AES", "authenticationToken": "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=8130520b-c116-45a9-824e-4a0082f3cb3c&Audience=urn%3atest&ExpiresOn=1450207516&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=eV7HDgZ9msp9H9bnEPGN91sBdU7XsZ9OyB6VgFhKBAU%3d"}]}'
        />
    </video>
or

```javascript
    var myPlayer = amp("vid1", /* Options */);
    myPlayer.src([{
        src: "//example/path/to/myVideo.ism/manifest",
        type: "application/vnd.ms-sstr+xml",
        protectionInfo: [{
            type: "PlayReady",
            authenticationToken: "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=d5646e95-63ee-4fbe-ba4e-295c8d9502e0&Audience=urn%3atest&ExpiresOn=1450222961&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=4Jop3kNJdzVI8L5IZLgFtPdImyE%2fHTRil0x%2bEikSdPs%3d"
        }] }, ]
    );
```

vagy több DRM-mel

```javascript
    var myPlayer = amp("vid1", /* Options */);
    myPlayer.src([{
        src: "//example/path/to/myVideo.ism/manifest",
        type: "application/vnd.ms-sstr+xml",
        protectionInfo: [{
                type: "PlayReady",
                authenticationToken: "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=d5646e95-63ee-4fbe-ba4e-295c8d9502e0&Audience=urn%3atest&ExpiresOn=1450222961&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=4Jop3kNJdzVI8L5IZLgFtPdImyE%2fHTRil0x%2bEikSdPs%3d"
            },
            {
                type: "Widevine",
                authenticationToken: "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=d5646e95-63ee-4fbe-ba4e-295c8d9502e0&Audience=urn%3atest&ExpiresOn=1450222961&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=4Jop3kNJdzVI8L5IZLgFtPdImyE%2fHTRil0x%2bEikSdPs%3d"
            },
            {
                   type: "FairPlay",
                  certificateUrl: "//example/path/to/myFairplay.der",
                   authenticationToken: "Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1cm46bWljcm9zb2Z0OmF6dXJlOm1lZGlhc2VydmljZXM6Y29udGVudGtleWlkZW50aWZpZXIiOiIyMTI0M2Q2OC00Yjc4LTRlNzUtYTU5MS1jZWMzMDI0NDNhYWMiLCJpc3MiOiJodHRwOi8vY29udG9zbyIsImF1ZCI6InVybjp0ZXN0IiwiZXhwIjoxNDc0NTkyNDYzLCJuYmYiOjE0NzQ1ODg1NjN9.mE7UxgNhkieMMqtM_IiYQj-FK1KKIzB6lAptw4Mi67A"
        }] } ]
    );
```

> [!NOTE]
> Nem minden böngésző/platform képes lejátszani a védett tartalmat. A támogatott adatokkal kapcsolatos további információkért tekintse meg a [lejátszási technológia](azure-media-player-playback-technology.md) című szakaszt.
> [!IMPORTANT]
> A lejátszóba átadott jogkivonat biztonságos tartalomra van kialakítva, és csak hitelesített felhasználók számára használható. Feltételezzük, hogy az alkalmazás SSL-t vagy más biztonsági mértéket használ. Emellett a végfelhasználónak megbízhatónak kell lennie, hogy ne assummed a jogkivonatot; Ha ez nem igaz, vonja be biztonsági szakértőit.

## <a name="next-steps"></a>Következő lépések ##

- [Azure Media Player rövid útmutató](azure-media-player-quickstart.md)