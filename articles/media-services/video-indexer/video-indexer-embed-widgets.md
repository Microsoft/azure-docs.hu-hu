---
title: Video Indexer widgetek beágyazása az alkalmazásokba
titleSuffix: Azure Media Services
description: Megtudhatja, hogyan ágyazhat be Video Indexer widgeteket az alkalmazásaiba.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/25/2021
ms.author: juliako
ms.custom: devx-track-js
ms.openlocfilehash: b13086e11e1181bba91a3255e68e9f8a32e78450
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98797788"
---
# <a name="embed-video-indexer-widgets-in-your-apps"></a>Video Indexer widgetek beágyazása az alkalmazásokba

Ebből a cikkből megtudhatja, hogyan ágyazhat be Video Indexer widgeteket az alkalmazásaiba. A Video Indexer háromféle vezérlő beágyazását támogatja az alkalmazásokba: *kognitív elemzési*, *lejátszási* és *szerkesztési*.

A 2. verziótól kezdődően a widget alap URL-címe tartalmazza a megadott fiók régióját. Például az USA nyugati régiójában lévő fiók a következőt hozza létre: `https://www.videoindexer.ai/embed/insights/.../?location=westus2` .

## <a name="widget-types"></a>Vezérlőtípusok

### <a name="cognitive-insights-widget"></a>Kognitív elemzési vezérlő

A Kognitív elemzési vezérlő az összes vizuális elemzést tartalmazza, amely a videóindexelési folyamat során lett kinyerve. A kognitív bepillantások widget a következő opcionális URL-paramétereket támogatja:

|Name|Definíció|Leírás|
|---|---|---|
|`widgets` | Vesszővel elválasztott sztringek | Lehetővé teszi a megjeleníteni kívánt adatfelismerések szabályozását.<br/>Példa: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords` a csak a felhasználók és a kulcsszavak felhasználói felületi felismeréseit jeleníti meg.<br/>Elérhető lehetőségek: személyek, animatedCharacters, kulcsszavak, címkék, érzelmek, érzelmek, témakörök, kulcsképek, átiratok, OCR, hangszórók, jelenetek és namedEntities.|
|`controls`|Vesszővel elválasztott sztringek|Lehetővé teszi a megjeleníteni kívánt vezérlők szabályozását.<br/>Példa: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?controls=search,download` csak a keresési lehetőség és a letöltés gomb megjelenítése.<br/>Elérhető lehetőségek: keresés, letöltés, előzetes beállítás, nyelv.|
|`language`|Rövid nyelvi kód (nyelv neve)|Az ellenőrzési nyelv szabályozása.<br/>Például: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=es-es` <br/>vagy `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=spanish`|
|`locale` | Egy rövid nyelvi kód | A felhasználói felület nyelvének szabályozása. Az alapértelmezett érték `en`. <br/>Példa: `locale=de`.|
|`tab` | Az alapértelmezett kijelölt lap | Az alapértelmezés  szerint megjelenített adatvizsgálatok lapot vezérli. <br/>Példa: `tab=timeline` a bepillantást jeleníti meg az **Idősor** lapon.|
|`location` ||A `location` paraméternek szerepelnie kell a beágyazott hivatkozásokban. lásd: a [régió nevének beolvasása](regions.md). Ha a fiókja előzetes verzióban érhető el, a `trial` értéket a hely értékére kell használni. `trial` a paraméter alapértelmezett értéke `location` .| 

### <a name="player-widget"></a>Lejátszó vezérlő

A Player widget Adaptív átviteli sebesség használatával is továbbíthatja a videót. A Player widget a következő opcionális URL-paramétereket támogatja.

|Name|Definíció|Leírás|
|---|---|---|
|`t` | Másodperc az elejétől | A lejátszó a megadott időpontból kezdi a lejátszást.<br/> Példa: `t=60`. |
|`captions` | Nyelvi kód | A megadott nyelven beolvassa a feliratot a widget betöltésével, hogy elérhető legyen a **feliratok** menüben.<br/> Példa: `captions=en-US`. |
|`showCaptions` | Logikai érték | Beállítja, hogy a lejátszó betöltésekor a feliratok megjelenítése már engedélyezve legyen.<br/> Példa: `showCaptions=true`. |
|`type`| | Aktiválja a hanglejátszó bőrét (a videó rész törlődik).<br/> Példa: `type=audio`. |
|`autoplay` | Logikai érték | Azt jelzi, hogy a játékosnak be kell-e játszania a videót a betöltéskor. Az alapértelmezett érték `true`.<br/> Példa: `autoplay=false`. |
|`language`/`locale` | Nyelvi kód | A lejátszó nyelvének szabályozása. Az alapértelmezett érték `en-US`.<br/>Példa: `language=de-DE`.|
|`location` ||A `location` paraméternek szerepelnie kell a beágyazott hivatkozásokban. lásd: a [régió nevének beolvasása](regions.md). Ha a fiókja előzetes verzióban érhető el, a `trial` értéket a hely értékére kell használni. `trial` a paraméter alapértelmezett értéke `location` .| 

### <a name="editor-widget"></a>Szerkesztői widget

A szerkesztői widgettel új projekteket hozhat létre, és kezelheti a videókban észlelt eredményeket. A szerkesztői widget a következő opcionális URL-paramétereket támogatja.

|Name|Definíció|Leírás|
|---|---|---|
|`accessToken`<sup>*</sup> | Sztring | Hozzáférést biztosít azokhoz a videókhoz, amelyek csak a widget beágyazásához használt fiókban találhatók.<br> A szerkesztő widgethez a `accessToken` paraméter szükséges. |
|`language` | Nyelvi kód | A lejátszó nyelvének szabályozása. Az alapértelmezett érték `en-US`.<br/>Példa: `language=de-DE`. |
|`locale` | Egy rövid nyelvi kód | Az adatfelismerés nyelvét vezérli. Az alapértelmezett érték `en`.<br/>Példa: `language=de`. |
|`location` ||A `location` paraméternek szerepelnie kell a beágyazott hivatkozásokban. lásd: a [régió nevének beolvasása](regions.md). Ha a fiókja előzetes verzióban érhető el, a `trial` értéket a hely értékére kell használni. `trial` a paraméter alapértelmezett értéke `location` .| 

<sup>*</sup>A tulajdonosnak körültekintően kell megadnia `accessToken` .

## <a name="embedding-videos"></a>Videók beágyazása

Ebből a szakaszból megtudhatja, hogyan ágyazhatja be a nyilvános és a magánjellegű tartalmakat az alkalmazásokba.

A `location` paraméternek szerepelnie kell a beágyazott hivatkozásokban. lásd: a [régió nevének beolvasása](regions.md). Ha a fiókja előzetes verzióban érhető el, a `trial` értéket a hely értékére kell használni. `trial` a paraméter alapértelmezett értéke `location` . Példa: `https://www.videoindexer.ai/accounts/00000000-0000-0000-0000-000000000000/videos/b2b2c74b8e/?location=trial`.

> [!IMPORTANT]
> Ha megoszt egy hivatkozást a **lejátszóhoz** **vagy az** adatelérési eszközhöz, a hozzáférési jogkivonatot fogja tartalmazni, és megadja a csak olvasási jogosultságokat a fiókjához.

### <a name="public-content"></a>Nyilvános tartalom

1. Jelentkezzen be a [video Indexer](https://www.videoindexer.ai/) webhelyére.
1. Válassza ki a használni kívánt videót, és nyomja meg a **Lejátszás** gombot.
1. Válassza ki a kívánt widget típusát (**kognitív ismeretek**, **lejátszó** vagy **szerkesztő**).
1. Kattintson a **&lt; / &gt; beágyazás** gombra.
5. Másolja a beágyazási kódot (ekkor megjelenik a **beágyazott kód másolása** a **megosztás & beágyazási** párbeszédpanelen).
6. Adja hozzá a kódot az alkalmazáshoz.

### <a name="private-content"></a>Magánjellegű tartalom

Privát videó beágyazásához át kell adnia egy hozzáférési jogkivonatot az `src` iframe-attribútumban:

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
    
Az alábbi módszerek egyikével szerezheti be a kognitív ismereteket ismertető widget tartalmát:

- Az eredmények [lekérése widget](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) API.<br/>
- A [videó-hozzáférési jogkivonat beolvasása](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Access-Token?). Adja hozzá lekérdezési paraméterként az URL-címhez. Ezt az URL-címet állítsa `src` be az IFRAME értékeként a korábban látható módon.

A beágyazott widget szerkesztési információinak megadásához olyan hozzáférési jogkivonatot kell átadnia, amely szerkesztési engedélyeket tartalmaz. Használja az információ [beolvasása widgetet](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) vagy a [videó-hozzáférési token beszerzése elemet](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Access-Token?) `&allowEdit=true` .

## <a name="widgets-interaction"></a>Vezérlőinterakciók

A kognitív betekintő widget interakcióba léphet az alkalmazás egy videóján. Ez a szakasz bemutatja, hogy ez a kommunikáció hogyan valósítható meg.

![Kognitív bepillantások widget Video Indexer](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="flow-overview"></a>A flow áttekintése

Az átiratok szerkesztésekor a következő folyamat következik be:

1. Az idővonalon szerkeszti a átiratot.
1. Video Indexer beolvassa ezeket a frissítéseket, és menti azokat a nyelvi modell [feladói átiratának szerkesztésében](customize-language-model-with-website.md#customize-language-models-by-correcting-transcripts) .
1. A feliratok frissülnek:

    * Ha Video Indexer Player widgetjét használja, az automatikusan frissül.
    * Ha külső lejátszót használ, a **képfeliratok beolvasása** hívással egy új feliratot kap a fájlhoz.

### <a name="cross-origin-communications"></a>Eltérő eredetű kommunikáció

Video Indexer widgetek más összetevőkkel való kommunikációjának lekéréséhez a Video Indexer szolgáltatás:

- A kereszthivatkozások közötti kommunikáció HTML5-módszerét használja `postMessage` .
- ellenőrzi az üzenetet a VideoIndexer.ai forráson.

Ha saját lejátszó kódot valósít meg, és integrálja a kognitív betekintő widgeteket, az Ön felelőssége, hogy ellenőrizze a VideoIndexer.ai származó üzenet eredetét.

### <a name="embed-widgets-in-your-app-or-blog-recommended"></a>Widgetek beágyazása az alkalmazásba vagy a blogjába (ajánlott)

Ez a szakasz azt mutatja be, hogyan lehet interakciót elérni két Video Indexer widget között, hogy amikor egy felhasználó kiválasztja a betekintési vezérlőt az alkalmazásban, a lejátszó a megfelelő pillanatra ugrik.

1. Másolja a Lejátszó vezérlő beágyazási kódját.
2. Másolja a Kognitív elemzési vezérlő beágyazási kódját.
3. Adja hozzá a [közvetítőfájlt](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js), amely a két vezérlő közötti kommunikációt kezeli:<br/> 
`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

Most, amikor egy felhasználó kiválasztja a betekintési vezérlőt az alkalmazásban, a lejátszó a megfelelő pillanatra ugrik.

További tudnivalókért tekintse meg a [widgetek video Indexer beágyazását bemutató](https://codepen.io/videoindexer/pen/NzJeOb)témakört.

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>A Kognitív elemzési vezérlő beágyazása és az Azure Media Player használata a tartalmak lejátszásához

Ez a szakasz bemutatja, hogyan lehet interakciót elérni egy kognitív bepillantási widget és egy Azure Media Player-példány között az [amp beépülő](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js)modullal.

1. Video Indexer beépülő modul hozzáadása az AMP-lejátszóhoz:<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. Azure Media Player példánya a Video Indexer beépülő modullal.

    ```javascript
    // Init the source.
    function initSource() {
        var tracks = [{
        kind: 'captions',
        // To load vtt from VI, replace it with your vtt URL.
        src: this.getSubtitlesUrl("c4c1ad4c9a", "English"),
        srclang: 'en',
        label: 'English'
        }];
        myPlayer.src([
        {
            "src": "//amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
            "type": "application/vnd.ms-sstr+xml"
        }
        ], tracks);
    }

    // Init your AMP instance.
    var myPlayer = amp('vid1', { /* Options */
        "nativeControlsForTouch": false,
        autoplay: true,
        controls: true,
        width: "640",
        height: "400",
        poster: "",
        plugins: {
        videobreakedown: {}
        }
    }, function () {
        // Activate the plug-in.
        this.videobreakdown({
        videoId: "c4c1ad4c9a",
        syncTranscript: true,
        syncLanguage: true,
        location: "trial" /* location option for paid accounts (default is trial) */
        });

        // Set the source dynamically.
        initSource.call(this);
    });
    ```

3. Másolja a Kognitív elemzési vezérlő beágyazási kódját.

Most már tud kommunikálni Azure Media Playerokkal.

További információkért tekintse meg a [Azure Media Player + VI bepillantást ismertető bemutatót](https://codepen.io/videoindexer/pen/rYONrO).

### <a name="embed-the-video-indexer-cognitive-insights-widget-and-use-a-different-video-player"></a>A Video Indexer kognitív betekintési widget beágyazása és más videolejátszó használata

Ha nem a Azure Media Playert használja, akkor manuálisan kell módosítania a videolejátszót a kommunikáció eléréséhez.

1. Szúrja be a videólejátszót.

    Szabványos HTML5-lejátszó például:

    ```html
    <video id="vid1" width="640" height="360" controls autoplay preload>
       <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
       Your browser does not support the video tag.
    </video>
    ```

2. Ágyazza be a Kognitív elemzési vezérlőt.
3. Implementálja a lejátszóval való kommunikációt az „üzenet” eseményre való figyeléssel. Például:

    ```javascript
    <script>
    
        (function(){
        // Reference your player instance.
        var playerInstance = document.getElementById('vid1');
        
        function jumpTo(evt) {
          var origin = evt.origin || evt.originalEvent.origin;
        
          // Validate that the event comes from the videobreakdown domain.
          if ((origin === "https://www.videobreakdown.com") && evt.data.time !== undefined){
                
            // Call your player's "jumpTo" implementation.
            playerInstance.currentTime = evt.data.time;
               
            // Confirm the arrival to us.
            if ('postMessage' in window) {
              evt.source.postMessage({confirm: true, time: evt.data.time}, origin);
            }
          }
        }
        
        // Listen to the message event.
        window.addEventListener("message", jumpTo, false);
          
        }())    
        
    </script>
    ```

További információkért tekintse meg a [Azure Media Player + VI bepillantást ismertető bemutatót](https://codepen.io/videoindexer/pen/YEyPLd).

## <a name="adding-subtitles"></a>Feliratok hozzáadása

Ha saját [Azure Media Player](https://aka.ms/azuremediaplayer)segítségével ágyaz be video Indexer-betekintést, a `GetVttUrl` metódussal lezárt feliratokat (feliratokat) is lekérhet. A JavaScript-metódust a Video Indexer AMP beépülő modulból is meghívhatja `getSubtitlesUrl` (a korábban látható módon).

## <a name="customizing-embeddable-widgets"></a>Beágyazható vezérlők testreszabása

### <a name="cognitive-insights-widget"></a>Kognitív elemzési vezérlő

Kiválaszthatja, hogy milyen típusú bepillantást szeretne. Ehhez adja meg azokat értékként a következő URL-paraméterhez, amelyet a rendszer a beolvasott beágyazási kódhoz adott hozzá (az API-ból vagy a webalkalmazásból): `&widgets=<list of wanted widgets>` .

A lehetséges értékek a következők:,,,,,,,,,,, `people` `animatedCharacters` `keywords` `labels` `sentiments` `emotions` `topics` `keyframes` `transcript` `ocr` `speakers` `scenes` és `namedEntities` .

Ha például olyan widgetet szeretne beágyazni, amely csak személyeket és kulcsszavakat tartalmaz, az iframe Beágyazási URL-címe a következőképpen fog kinézni:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords`

Az IFRAME-ablak címe testreszabható az IFRAME URL-cím megadásával is `&title=<YourTitle>` . (Testreszabja a HTML `<title>` -értéket).
   
Ha például azt szeretné, hogy az IFRAME-ablak a "MyInsights" címet adja meg, az URL-cím a következőhöz hasonló lesz:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

Fontos tudni, hogy ez a beállítás csak olyan esetekben releváns, ha azt szeretné, hogy az elemzések új ablakban nyíljanak meg.

### <a name="player-widget"></a>Lejátszó vezérlő

A Video Indexer-lejátszó beágyazásakor megadhatja a lejátszó méretét az iframe méretének meghatározásával.

Például:

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

Alapértelmezés szerint az Video Indexer-lejátszó automatikusan létrehozta a videó átiratán alapuló zárt feliratokat. A videóból kinyert átirat a videó feltöltésekor kiválasztott forrás nyelvét jelöli.

Ha más nyelvű beágyazást szeretne, hozzáadhat `&captions=<Language Code>` a beágyazási lejátszó URL-címéhez. Ha azt szeretné, hogy a feliratok alapértelmezés szerint megjelenjenek, &showCaptions = True értéket adhat át.

A beágyazási URL-cím ezután a következőképpen fog kinézni:

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=en-us`

#### <a name="autoplay"></a>Robotpilota

Alapértelmezés szerint a lejátszó elkezdi lejátszani a videót. `&autoplay=false`az előző beágyazási URL-címre való átadással dönthet úgy, hogy nem.

## <a name="code-samples"></a>Kódminták

Tekintse meg a [Code Samples](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Embedding%20widgets) -tárházat, amely mintákat tartalmaz video Indexer API-hoz és widgetekhez:

| Fájl/mappa                       | Leírás                                |
|-----------------------------------|--------------------------------------------|
| `azure-media-player`              | Videó-indexelő videó betöltése egyéni Azure Media Playerban.                        |
| `azure-media-player-vi-insights`  | A VI-betekintést egyéni Azure Media Player ágyazhatja be.                             |
| `control-vi-embedded-player`      | A VI-lejátszó beágyazása és felügyelete kívülről.                                    |
| `custom-index-location`           | A VI-betekintést egy egyéni külső helyről ágyazhatja be (ez lehet az ügyfél egy blobja).     |
| `embed-both-insights`             | A VI bepillantást nyerhet a játékosokra és az adatokra.                            |
| `embed-insights-with-AMP`         | A VI-betekintési widget beágyazása egyéni Azure Media Player.                      |
| `customize-the-widgets`           | A VI widgetek beágyazása testreszabott beállításokkal.                                     |
| `embed-both-widgets`              | A VI-lejátszó és-információk beágyazása és a velük való kommunikáció.                      |
| `url-generator`                   | Egyéni beágyazási URL-címeket hoz létre a felhasználó által megadott beállítások alapján.             |
| `html5-player`                    | A VI-betekintést egy alapértelmezett HTML5-videolejátszó alá ágyazhatja.                           |

## <a name="supported-browsers"></a>Támogatott böngészők

További információ: [támogatott böngészők](video-indexer-overview.md#supported-browsers).

## <a name="next-steps"></a>Következő lépések

Video Indexer információk megtekintésével és szerkesztésével kapcsolatos információkért lásd: [video Indexer-információk megtekintése és szerkesztése](video-indexer-view-edit.md).

Tekintse meg a [video Indexer CodePen](https://codepen.io/videoindexer/pen/eGxebZ)is.
