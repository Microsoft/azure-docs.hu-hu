---
title: Azure Media Player teljes telepítés
description: Ismerje meg, hogyan állíthatja be a Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/20/2020
ms.custom: devx-track-js
ms.openlocfilehash: e6ec59e3cb8d285652935b42493c820a7379eeb8
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "104956077"
---
# <a name="azure-media-player-full-setup"></a>Az Azure Media Player teljes telepítése #

A Azure Media Player egyszerűen beállítható. Csak néhány percet vesz igénybe, hogy a médiatartalom alapszintű lejátszása közvetlenül a Azure Media Services-fiókból történjen. A [mintákat](https://github.com/Azure-Samples/azure-media-player-samples) a kiadás minták könyvtára is megadja.

## <a name="step-1-include-the-javascript-and-css-files-in-the-head-of-your-page"></a>1. lépés: a JavaScript-és CSS-fájlok belefoglalása az oldal élére ##

A Azure Media Player használatával a CDN által üzemeltetett verzióból férhet hozzá a szkriptekhez. Azt javasoljuk, hogy a JavaScriptet még a befejezést megelőzően `<body>` `<head>` , de az Azure Meia Player tartalmaz egy "HTML5 szilánkot", amely a régebbi IE-verziók számára szükséges, hogy a videó címkéjét érvényes elemként vegye figyelembe.

> [!NOTE]
> Ha már használ egy HTML5-szilánkot, például a [modernizációt](https://modernizr.com/) , bárhol is használhatja a Azure Media Player JavaScriptet. Azonban győződjön meg arról, hogy a modernizáló verziója tartalmazza a szilánkot a videóhoz.

### <a name="cdn-version"></a>CDN-verzió ###

```html
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <script src= "//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
```

> [!IMPORTANT]
> A verziót **ne** használja `latest` éles környezetben, mert az igény szerint változhat. Cserélje le a helyére a `latest` Azure Media Player egy verzióját. Például cserélje le a következőt: `latest` `2.1.1` . Azure Media Player verziók lekérdezése [innen](azure-media-player-changelog.md)lehetséges.

> [!NOTE]
> Mivel a `1.2.0` kiadás már nem szükséges ahhoz, hogy a tartalék technológiák helyére belekerüljön a hely, a rendszer automatikusan felveszi a helyet a azuremediaplayer.min.js fájl relatív elérési útjából. A tartalék technológiák helyét úgy módosíthatja, hogy a fenti szkriptek után hozzáadja a következő parancsfájlt `<head>` .

> [!NOTE]
> A Flash és a Silverlight beépülő modul jellegéből adódóan az SWF-és a XAP-fájlokat bizalmas információ vagy adatok nélkül kell üzemeltetni, ezért a rendszer automatikusan gondoskodik az Azure CDN üzemeltetett verzióról.

```javascript
    <script>
      amp.options.flashSS.swf = "//amp.azure.net/libs/amp/latest/techs/StrobeMediaPlayback.2.0.swf"
      amp.options.silverlightSS.xap = "//amp.azure.net/libs/amp/latest/techs/SmoothStreamingPlayer.xap"
    </script>
```

## <a name="step-2-add-an-html5-video-tag-to-your-page"></a>2. lépés: HTML5-videó címke hozzáadása az oldalhoz ##

A Azure Media Player használatával a videó beágyazására használható HTML5-videó címkével. A Azure Media Player ezután elolvassa a címkét, és az összes böngészőben, nem csak a HTML5-videót támogató böngészőkben fog működni. Az alapszintű jelölésen felül Azure Media Player néhány további részletre van szükség.

1. Az `<data-setup>` attribútum az `<video>` Azure Media Playeron automatikusan beállítja a videót, amikor az oldal elkészült, és az attribútumból beolvassa az összes (JSON formátumban).
1. A `id` következő attribútumot kell használni, és egyedinek kell lennie minden videóhoz ugyanazon a lapon.
1. Az `class` attribútum két osztályt tartalmaz:
    - `azuremediaplayer` a Azure Media Player felhasználói felület funkcióinak használatához szükséges stílusokat alkalmazza.
    - `amp-default-skin` az alapértelmezett bőr alkalmazása a HTML5-vezérlőkre
1. A `<source>` két kötelező attribútumot tartalmaz
    - `src` az attribútum tartalmazhatja a **. ISM/manifest* fájlt Azure Media Services hozzáadásával, Azure Media Player automatikusan HOZZÁADJA a Dash, a Smooth és a HLS URL-címeit a lejátszóhoz.
    - `type` az attribútum a stream kötelező MIME-típusa. A *". ISM/manifest"* kifejezéshez társított MIME-típus az *"Application/vnd. MS-sstr + XML"* .
1. Az  `<data-setup>` Azure Media Player nem kötelező attribútuma, `<source>` Ha a streamhez egyedi kézbesítési szabályzatok tartoznak Azure Media Services, beleértve a (z) titkosítási típust (AES vagy PlayReady, Widevine vagy Fairplay) és a tokent.

Az attribútumok, a beállítások, a források és a nyomon követések pontosan úgy foglalhatók el/zárhatók ki, mint a HTML5-videóknál.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" autoplay controls width="640" height="400" poster="poster.jpg" data-setup='{"techOrder": ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"], "nativeControlsForTouch": false}'>
        <source src="http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest" type="application/vnd.ms-sstr+xml" />
        <p class="amp-no-js">
            To view this video please enable JavaScript, and consider upgrading to a web browser that supports HTML5 video
        </p>
    </video>
```

Alapértelmezés szerint a nagyméretű Lejátszás gomb a bal felső sarokban található, így nem fedi fel a plakát érdekes részeit. Ha inkább a nagy lejátszás gombot szeretné központilag felvenni, hozzáadhat egy további elemet is az `amp-big-play-centered` `class` `<video>` elemhez.

### <a name="alternative-setup-for-dynamically-loaded-html"></a>Alternatív beállítás a dinamikusan betöltött HTML-fájlokhoz ###

Ha a weblap vagy alkalmazás dinamikusan betölti a videó címkéjét (Ajax, appendChild stb.), így előfordulhat, hogy az oldal betöltésekor nem létezik, manuálisan kell beállítania a lejátszót ahelyett, hogy az adattelepítési attribútumra kellene támaszkodnia. Ehhez először távolítsa el az adatbeállítási attribútumot a címkéből, hogy ne legyen félreértés a lejátszó inicializálásakor. Ezután futtassa a következő JavaScriptet egy kis idő elteltével a Azure Media Player JavaScript betöltését követően, és miután betöltötte a videó címkét a DOM-ba.

```javascript
    var myPlayer = amp('vid1', { /* Options */
            techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"],
            "nativeControlsForTouch": false,
            autoplay: false,
            controls: true,
            width: "640",
            height: "400",
            poster: ""
        }, function() {
              console.log('Good to go!');
               // add an event listener
              this.addEventListener('ended', function() {
                console.log('Finished!');
            });
          }
    );
    myPlayer.src([{
        src: "http://samplescdn.origin.mediaservices.windows.net/e0e820ec-f6a2-4ea2-afe3-1eed4e06ab2c/AzureMediaServices_Overview.ism/manifest",
        type: "application/vnd.ms-sstr+xml"
    }]);
```

A függvény első argumentuma `amp` a videó címkéjének azonosítója. Cserélje le saját.

A második argumentum egy Options objektum. Lehetővé teszi további beállítások megadását, például az adattelepítő attribútumot.

A harmadik argumentum egy "Ready" visszahívás. Azure Media Player inicializálása után ez a függvényt hívja meg. A Ready visszahívás esetében a "this" objektum a Player-példányra hivatkozik.

Az elem AZONOSÍTÓjának használata helyett az elemre mutató hivatkozást is át lehet adni.

```javascript

    amp(document.getElementById('example_video_1'), {/*Options*/}, function() {
        //This is functionally the same as the previous example.
    });
    myPlayer.src([{ src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml"]);
```

## <a name="next-steps"></a>Következő lépések ##

- [Azure Media Player rövid útmutató](azure-media-player-quickstart.md)
