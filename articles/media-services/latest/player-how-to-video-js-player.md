---
title: A Video.js lejátszó használata a Azure Media Services
description: Ez a cikk azt ismerteti, hogyan használható a HTML-videó objektum és a JavaScript a Azure Media Services
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
ms.openlocfilehash: 0ac00ecbbe5e0a72bccf6effe5e82fc7d973c91e
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106281837"
---
# <a name="how-to-use-the-videojs-player-with-azure-media-services"></a>Az Video.js-lejátszó használata a Azure Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="overview"></a>Áttekintés

A Video.js egy HTML5-világra épülő webes videolejátszó. Az adaptív adathordozó-formátumokat (például KÖTŐJELet és HLS) egy böngészőben, beépülő modulok vagy flash használata nélkül játssza le. Ehelyett Video.js az Open Web Standards MediaSource Extensions és a encrypted Media Extensions szolgáltatást használja. Emellett támogatja a videolejátszás asztali számítógépeken és mobileszközökön.

A hivatalos dokumentációja a következő címen érhető el: [https://docs.videojs.com/](https://docs.videojs.com/) .

## <a name="sample-code"></a>Mintakód
A cikk mintakód a következő helyen érhető el: [Azure-Samples/Media-Services-3rdparty-Player-Samples](https://github.com/Azure-Samples/media-services-3rdparty-player-samples).

## <a name="implement-the-player"></a>A lejátszó implementálása

1. Hozzon létre egy `index.html` fájlt, ahol a lejátszót fogja üzemeltetni. Adja hozzá a következő kódrészleteket (az újabb verziókat lecserélheti, ha van ilyen):

    ```html
    <html>
      <head>
        <link href="https://vjs.zencdn.net/7.8.2/video-js.css" rel="stylesheet" />
      </head>
      <body>
        <video id="video" class="video-js vjs-default-skin vjs-16-9" controls data-setup="{}">
        </video>

        <script src="https://vjs.zencdn.net/7.8.2/video.js"></script>
        <script src="https://cdn.jsdelivr.net/npm/videojs-contrib-eme@3.7.0/dist/videojs-contrib-eme.min.js"></script>
        <script type="module" src="index.js"></script>
      </body>
    <html>
    ```

2. Adjon hozzá egy `index.js` fájlt a következő kóddal:

    ```javascript
    var videoJS = videojs("video");
    videoJS.src({
      src: "manifestUrl",
      type: "protocolType",
    });
    ```

3. Cserélje le az `manifestUrl` elemet a HLS vagy a kötőjel URL-címére az eszköz streaming-lokátorán, amely a Azure Portal folyamatos átviteli lokátor lapján található.
    ![streaming Locator URL-címei](media/player-shaka-player-how-to/streaming-urls.png)

4. Cserélje le `protocolType` a kapcsolót a következő beállításokra:

    - "application/x-mpegURL" HLS-protokollokhoz
    - "Application/Dash + XML" a kötőjel protokollokhoz

### <a name="set-up-captions"></a>Feliratok beállítása

Futtassa a `addRemoteTextTrack` metódust, és cserélje le:

- `subtitleKind``"captions"` `"subtitles"` `"descriptions"` vagy`"metadata"`  
- `caption` a. VTT fájl elérési útjának (a VTT-fájlnak ugyanabban a gazdagépen kell lennie, hogy elkerülje a CORS-hibát)
- `subtitleLang` a BCP 47 kóddal, például `"eng"` angol vagy `"es"` spanyol nyelven
- `subtitleLabel` a felirat kívánt megjelenítendő nevével

```javascript
videojs.players.video.addRemoteTextTrack({
  kind: subtitleKind,
  src: caption,
  srclang: subtitleLang,
  label: subtitleLabel
});
```

### <a name="set-up-token-authentication"></a>Jogkivonat-hitelesítés beállítása

A jogkivonatot a kérelem fejlécének engedélyezés mezőjében kell megadni. A CORS kapcsolatos problémák elkerülése érdekében ezt a jogkivonatot csak az URL-címében szereplő kérelmekben kell beállítani `'keydeliver'` . A következő kódrészleteknek kell elvégezniük a munkát:

```javascript
setupTokenForDecrypt (options) {
  if (options.uri.includes('keydeliver')) {
    options.headers = options.headers || {}
    options.headers.Authorization = 'Bearer=' + this.getInputToken()
  }

  return options
}
```

Ezután a fenti függvényt csatolni kell az `videojs.Hls.xhr.beforeRequest` eseményhez.

```javascript
videojs.Hls.xhr.beforeRequest = setupTokenForDecrypt;
```

### <a name="set-up-aes-128-encryption"></a>AES-128 titkosítás beállítása

A Video.js további konfiguráció nélkül támogatja az AES-128 titkosítást. 

> [!NOTE] 
> Jelenleg [probléma](https://github.com/videojs/video.js/issues/6717) van a titkosítással és a HLS/Dash CMAF-tartalommal, amelyek nem játszhatók le.

### <a name="set-up-drm-protection"></a>A DRM-védelem beállítása

A DRM-védelem támogatásához hozzá kell adnia a videojs-a [-feltörzs-eme](https://github.com/videojs/videojs-contrib-eme) hivatalos bővítményt. A CDN verziója is működik.

1. A `index.js` fent részletezett fájlban a `videoJS.eme();` videó forrásának hozzáadása *előtt* adja meg az eme-bővítményt:

   ```javascript
    videoJS.eme();
   ```

2. Most megadhatja a DRM-szolgáltatások URL-címeit és a megfelelő licencek URL-címeit az alábbiak szerint:

   ```javascript
   videoJS.src({
       keySystems: {
           "com.microsoft.playready": "YOUR PLAYREADY LICENSE URL",
           "com.widevine.alpha": "YOUR WIDEVINE LICENSE URL",
           "com.apple.fps.1_0": {
            certificateUri: "YOUR FAIRPLAY CERTIFICATE URL",
            licenseUri: "YOUR FAIRPLAY LICENSE URL"
           }
         }
       })

   ```

#### <a name="acquiring-the-license-url"></a>A licenc URL-címének beszerzése

A licenc URL-címének megvásárlásához a következőket teheti:

- A DRM-szolgáltató konfigurálása
- Ha a mintát használja, tekintse `output.json` meg a VODs *setup-vod.ps1* PowerShell-parancsfájl futtatásakor generált dokumentumot, vagy az élő streamek *start-live.ps1* parancsfájlját. Emellett a fájlon belül is megtalálja a gyerekeket.

#### <a name="using-tokenized-drm"></a>Jogkivonat-kezelő DRM használata

A jogkivonattal rendelkező DRM-védelem támogatásához hozzá kell adnia a következő sort a `src` lejátszó tulajdonságához:

```javascript
videoJS.src({
src: ...,
emeHeaders: {'Authorization': "Bearer=" + "YOUR TOKEN"},
keySystems: {...
```

## <a name="next-steps"></a>Következő lépések

- [A Azure Media Player használata](../azure-media-player/azure-media-player-overview.md)  
- [Gyors útmutató: tartalom titkosítása](drm-encrypt-content-how-to.md)
