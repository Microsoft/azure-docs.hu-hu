---
title: A Azure Media Services használatával
description: Ez a cikk azt ismerteti, hogyan használható a Azure Media Services
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-js
ms.openlocfilehash: 9699e6ad3f004f94c83440dd39f13c6db887e224
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106281824"
---
# <a name="how-to-use-the-shaka-player-with-azure-media-services"></a>A Azure Media Services használatával

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="overview"></a>Áttekintés

A videotár-lejátszó egy nyílt forráskódú JavaScript-kódtár az adaptív adathordozók számára. Az adaptív adathordozó-formátumokat (például KÖTŐJELet és HLS) egy böngészőben, beépülő modulok vagy flash használata nélkül játssza le. Ehelyett a következő a nyílt webes szabványok Media Source Extensions and encrypted Media Extensions használatával működik.

Javasoljuk, hogy a [Mux.jst ](https://github.com/videojs/mux.js/) , anélkül, hogy ez lenne, a HLS CMAF-formátumot kellene használni, de nem HLS TS-t.

A hivatalos dokumentációja a következő címen érhető [el: a](https://shaka-player-demo.appspot.com/docs/api/tutorial-welcome.html)(z).

## <a name="sample-code"></a>Mintakód

A cikk mintakód a következő helyen érhető el: [Azure-Samples/Media-Services-3rdparty-Player-Samples](https://github.com/Azure-Samples/media-services-3rdparty-player-samples).

## <a name="implementing-the-player"></a>A lejátszó implementálása

Kövesse ezeket az utasításokat, ha a lejátszó saját példányát kell megvalósítani:

1. Hozzon létre egy `index.html` fájlt, ahol a lejátszót fogja üzemeltetni. Adja hozzá a következő kódrészleteket (az újabb verziókat lecserélheti, ha van ilyen):

    ```html
    <html>
      <head>
        <script src="//cdn.jsdelivr.net/npm/shaka-player@3.0.1/dist/shaka-player.compiled.js"></script>
        <script src="//cdn.jsdelivr.net/npm/mux.js@5.6.3/dist/mux.js"></script>
        <script type="module" src="index.js"></script>
      </head>
      <body>
        <video id="video" controls></video>
      </body>
    </html>
    ```

1. Adjon hozzá egy JavaScript-fájlt a következő kóddal:

    ```javascript
    // myScript.js
    shaka.polyfill.installAll();

    var video = document.getElementById('video');
    var player = new shaka.Player(video);
    window.player = player;

    var manifestUrl = 'https://amsplayeraccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/sample-vod.ism/manifest(format=m3u8-aapl)';
    player.load(manifestUrl);
    ```

1. Cserélje le az `manifestUrl` elemet az eszköz HLS vagy kötőjel URL-címére, amely a Azure Portal folyamatos átviteli lokátor lapján található.
    ![streaming Locator URL-címei](media/player-shaka-player-how-to/streaming-urls.png)

1. Futtasson egy kiszolgálót (például: `npm http-server` ), és a lejátszójának működnie kell...

## <a name="set-up-captions"></a>Feliratok beállítása

### <a name="set-up-vod-captions"></a>VOD-feliratok beállítása

Futtassa az alábbi kódrészleteket, és cserélje le a helyére a `captionUrl` . VTT könyvtárat (a VTT-fájlnak ugyanabban a gazdagépen kell lennie, hogy elkerülje a CORS-hibát), `lang` a két betűs kóddal a nyelvhez, valamint `type` a vagy a következőkkel `caption` `subtitle` :

```javascript
player.configure('streaming.alwaysStreamText', true)
player.load(manifestUrl).then(function(){
        player.addTextTrack(captionUrl, lang, type, 'text/vtt');
        var tracks = player.getTextTracks();
        player.selectTextTrack(tracks[0]);
});
```

### <a name="set-up-live-stream-captions"></a>Élő stream-feliratok beállítása

Az élő adatfolyamban lévő feliratok engedélyezése a következő kódrészlet hozzáadásával állítható be:

```javascript
player.setTextTrackVisibility(true)
```

## <a name="set-up-token-authentication"></a>Jogkivonat-hitelesítés beállítása

Futtassa az alábbi kódrészleteket, és cserélje le a `token` elemet a token sztringre:

```javascript
player.getNetworkingEngine().registerRequestFilter(function (type, request) {
  if (type === shaka.net.NetworkingEngine.RequestType.LICENSE) {
    request.headers['Authorization'] = 'Bearer ' + token;
  }
});
```

## <a name="set-up-aes-128-encryption"></a>AES-128 titkosítás beállítása

A nem támogatja jelenleg az AES-128 titkosítást.

Egy GitHub- [hibára](https://github.com/google/shaka-player/issues/850) mutató hivatkozás, amely a funkció állapotát követi.

## <a name="set-up-drm-protection"></a>A DRM-védelem beállítása

A a a titkosított adathordozó-bővítményeket (EME) használja, ezért biztonságos URL-címet igényel. Így a DRM-védelemmel ellátott tartalmak teszteléséhez HTTPS-t kell használni. Ha a hely HTTPS protokollt használ, akkor a jegyzékfájlnak és minden szegmensnek is HTTPS protokollt kell használnia. Ez a vegyes tartalomra vonatkozó követelmények miatt van.

A licenckiszolgáló (ok) URL-címének (vagy) a (z) (k) elérési sorrendjének sorrendje:

1. A hibakereséshez használt ClearKey-konfiguráció felülbírálja az összes többi beállítást. (Az alkalmazás továbbra is megadhat egy ClearKey-licenckiszolgálót.)
2. Ha vannak ilyenek, az alkalmazás által konfigurált kiszolgálók felülbírálják a jegyzékfájlban lévő bármit.
3. A jegyzékfájlhoz megadott licenckiszolgálók csak akkor használhatók, ha nincs más megadva.

A Widevine vagy PlayReady tartozó licenckiszolgáló URL-címének megadásához a következő kódot használhatja:

```javascript
player.configure({
  drm: {
    servers: {
      "com.widevine.alpha": "YOUR WIDEVINE LICENSE URL",
      "com.microsoft.playready": "YOUR PLAYREADY LICENSE URL"
    }
  }
});

```

Minden FairPlay-tartalomhoz be kell állítani egy kiszolgálói tanúsítványt. A lejátszó konfigurációjában van beállítva:

```javascript
const req = await fetch("YOUR FAIRPLAY CERTIFICATE URL");
const cert = await req.arrayBuffer();
player.configure('drm.advanced.com\\.apple\\.fps\\.1_0.serverCertificate', new Uint8Array(cert));
```

További információkért lásd a a következő témakört: a a a a [Windows Media Player DRM Protection dokumentációja](https://shaka-player-demo.appspot.com/docs/api/tutorial-drm-config.html)

## <a name="next-steps"></a>Következő lépések

* [A Azure Media Player használata](../azure-media-player/azure-media-player-overview.md)
* [Gyors útmutató: tartalom titkosítása](drm-encrypt-content-how-to.md)
