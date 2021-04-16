---
title: Vezérlők Video Indexer beágyazása az alkalmazásokba
titleSuffix: Azure Media Services
description: Megtudhatja, hogyan ágyazhat Video Indexer widgeteket az alkalmazásokba.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/25/2021
ms.author: juliako
ms.custom: devx-track-js
ms.openlocfilehash: 822d50bca6bc1139e9b5f0554bcf9a56a8fcbd74
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532879"
---
# <a name="embed-video-indexer-widgets-in-your-apps"></a>Vezérlők Video Indexer beágyazása az alkalmazásokba

Ez a cikk bemutatja, hogyan ágyazhat Video Indexer widgeteket az alkalmazásokba. A Video Indexer háromféle vezérlő beágyazását támogatja az alkalmazásokba: *kognitív elemzési*, *lejátszási* és *szerkesztési*.

A 2. verziótól kezdődően a widget alap URL-címe tartalmazza a megadott fiók régióját. Az USA nyugati régiójában található fiók például a következőt hozza létre: `https://www.videoindexer.ai/embed/insights/.../?location=westus2` .

## <a name="widget-types"></a>Vezérlőtípusok

### <a name="cognitive-insights-widget"></a>Kognitív elemzési vezérlő

A Kognitív elemzési vezérlő az összes vizuális elemzést tartalmazza, amely a videóindexelési folyamat során lett kinyerve. A Cognitive Insights-widget a következő választható URL-paramétereket támogatja:

|Name|Definíció|Leírás|
|---|---|---|
|`widgets` | Vesszővel elválasztott sztringek | Lehetővé teszi a renderelni kívánt elemzések szabályozását.<br/>Példa: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords` csak személyeket és kulcsszavakat renderel felhasználói felületi elemzésekhez.<br/>Elérhető lehetőségek: people, animáltDiagramok, kulcsszavak, címkék, hangulatok, érzelmek, témakörök, kulcskeretek, átiratok, ocr, beszélők, jelenetek és namedEntities.|
|`controls`|Vesszővel elválasztott sztringek|Lehetővé teszi a renderelni kívánt vezérlők szabályozását.<br/>Például: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?controls=search,download` csak a keresési lehetőséget és a letöltési gombot rendereli.<br/>Elérhető lehetőségek: keresés, letöltés, beállításkészletek, nyelv.|
|`language`|Egy rövid nyelvi kód (nyelv neve)|Az elemzés nyelvét szabályozza.<br/>Például: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=es-es` <br/> vagy `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=spanish`|
|`locale` | Egy rövid nyelvi kód | A felhasználói felület nyelvét szabályozza. Az alapértelmezett érték `en`. <br/>Példa: `locale=de`.|
|`tab` | Az alapértelmezett kiválasztott lap | Az **alapértelmezés szerint** renderelt Insights lapot szabályozza. <br/>Példa: `tab=timeline` az elemzéseket úgy rendereli, hogy az **Idősor** lap van kiválasztva.|
|`location` ||A paraméternek szerepelnie kell a beágyazott hivatkozások között. Lásd a régió `location` [nevének lekért nevét.](regions.md) Ha a fiókja előzetes verzióban érhető el, a hely értékeként a értéket `trial` kell használni. `trial` A a paraméter alapértelmezett `location` értéke.| 

### <a name="player-widget"></a>Lejátszó vezérlő

A Lejátszó vezérlővel adaptív bitsebességet használó videót streamelhet. A Lejátszó widget a következő választható URL-paramétereket támogatja.

|Name|Definíció|Leírás|
|---|---|---|
|`t` | Másodpercek a kezdéstől | Elindítja a lejátszót a megadott időponttól.<br/> Példa: `t=60`. |
|`captions` | Egy nyelvi kód | Lekéri a megadott nyelvű feliratot a widget betöltésekor, hogy elérhető legyen a **Feliratok** menüben.<br/> Példa: `captions=en-US`. |
|`showCaptions` | Logikai érték | Beállítja, hogy a lejátszó betöltésekor a feliratok megjelenítése már engedélyezve legyen.<br/> Példa: `showCaptions=true`. |
|`type`| | Aktiválja a hanglejátszó hangot (a videó rész el lesz távolítva).<br/> Példa: `type=audio`. |
|`autoplay` | Logikai érték | Jelzi, hogy a lejátszónak el kell-e kezdenie a videó lejátszását a betöltéskor. Az alapértelmezett érték `true`.<br/> Példa: `autoplay=false`. |
|`language`/`locale` | Egy nyelvi kód | A lejátszó nyelvét szabályozza. Az alapértelmezett érték `en-US`.<br/>Példa: `language=de-DE`.|
|`location` ||A paraméternek szerepelnie kell a beágyazott hivatkozások között. Lásd a régió `location` [nevének lekért nevét.](regions.md) Ha a fiókja előzetes verzióban érhető el, a hely értékeként a értéket `trial` kell használni. `trial` A a paraméter alapértelmezett `location` értéke.| 

### <a name="editor-widget"></a>Szerkesztő widget

A Szerkesztő vezérlővel új projekteket hozhat létre, és kezelheti egy videó elemzését. Az Editor (Szerkesztő) widget a következő nem kötelező URL-paramétereket támogatja.

|Name|Definíció|Leírás|
|---|---|---|
|`accessToken`<sup>*</sup> | Sztring | Hozzáférést biztosít olyan videókhoz, amelyek csak a widget beágyazási fiókjában vannak.<br> A Szerkesztő widgethez szükség van a `accessToken` paraméterre. |
|`language` | Egy nyelvi kód | A lejátszó nyelvét szabályozza. Az alapértelmezett érték `en-US`.<br/>Példa: `language=de-DE`. |
|`locale` | Egy rövid nyelvi kód | Az elemzés nyelvét szabályozza. Az alapértelmezett érték `en`.<br/>Példa: `language=de`. |
|`location` ||A paraméternek szerepelnie kell a beágyazott hivatkozásokban. Lásd a régió `location` [nevének lekért nevét.](regions.md) Ha a fiókja előzetes verzióban érhető el, a hely értékeként a értéket `trial` kell használni. `trial` A a paraméter alapértelmezett `location` értéke.| 

<sup>*</sup>A tulajdonosnak körültekintően `accessToken` kell eljárnia.

## <a name="embed-videos"></a>Videók beágyazása

Ez a szakasz videók beágyazását ismerteti a portálon vagy az URL-cím alkalmazásokba [való manuális](#assemble-the-url-manually) összeállításával. [](#the-portal-experience) 

A paraméternek szerepelnie kell a beágyazott hivatkozásokban. Lásd a régió `location` [nevének lekért nevét.](regions.md) Ha a fiókja előzetes verzióban érhető el, a hely értékeként a értéket `trial` kell használni. `trial` A a paraméter alapértelmezett `location` értéke. Példa: `https://www.videoindexer.ai/accounts/00000000-0000-0000-0000-000000000000/videos/b2b2c74b8e/?location=trial`.

### <a name="the-portal-experience"></a>A portál felhasználói élménye

Videó beágyazása a portálon, az alábbiak szerint:

1. Jelentkezzen be az [Video Indexer](https://www.videoindexer.ai/) webhelyére.
1. Válassza ki a videót, amelyről dolgozni szeretne, majd nyomja le a **Play (Lejátszás) gombot.**
1. Válassza ki a kívánt widgettípust **(Cognitive Insights,** **Player**, vagy **Editor**).
1. Kattintson a **&lt; / &gt; Beágyazás elemre.**
5. Másolja ki a beágyazási  kódot (ez a Beágyazott kód másolása a Beágyazás megosztása **& párbeszédpanelen jelenik** meg).
6. Adja hozzá a kódot az alkalmazáshoz.

> [!NOTE]
> A Player vagy  **Insights** widget hivatkozásának megosztása magában foglalja a hozzáférési jogkivonatot, és csak olvasható engedélyeket ad a fiókjának.

### <a name="assemble-the-url-manually"></a>Az URL manuális összeállítása

#### <a name="public-videos"></a>Nyilvános videók

Az URL-címet összeállító nyilvános videókat a következőképpen ágyazhatja be:

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>`
  
  
#### <a name="private-videos"></a>Privát videók

Privát videó beágyazása érdekében át kell adni egy hozzáférési jogkivonatot (használja a [Videó](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Access-Token) lehívása hozzáférési jogkivonatot az `src` iframe attribútumában:

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
  
### <a name="provide-editing-insights-capabilities"></a>Elemzési képességek szerkesztése

Ahhoz, hogy elemzési képességeket biztosítson a beágyazott widgetben, át kell adnia egy hozzáférési jogkivonatot, amely szerkesztési engedélyeket is tartalmaz. Használja a [Get Video Access Token (Videó-hozzáférési jogkivonat lehívása) és](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Access-Token) a `&allowEdit=true` (jogkivonat) használatával

## <a name="widgets-interaction"></a>Vezérlőinterakciók

A Cognitive Insights-widget képes interakcióba lépni az alkalmazás videóival. Ez a szakasz bemutatja, hogy ez a kommunikáció hogyan valósítható meg.

![Cognitive Insights-vezérlő Video Indexer](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="flow-overview"></a>A Flow áttekintése

Az átiratok szerkesztésekor a következő folyamat történik:

1. Az átiratot az idővonalon szerkesztheti.
1. Video Indexer megkapja ezeket a frissítéseket, és [](customize-language-model-with-website.md#customize-language-models-by-correcting-transcripts) menti őket a nyelvi modell átiratának szerkesztésében.
1. A feliratok frissülnek:

    * Ha a lejátszó Video Indexer vezérlőt használja, az automatikusan frissül.
    * Ha külső lejátszót használ – egy új feliratfájl-felhasználót kap a **Videófeliratok lehívása** hívással.

### <a name="cross-origin-communications"></a>Eltérő eredetű kommunikáció

Ahhoz, Video Indexer vezérlők kommunikáljanak más összetevőkkel, a Video Indexer szolgáltatás:

- Az eredetközi kommunikáció HTML5-metódusát `postMessage` használja.
- ellenőrzi az üzenetet a VideoIndexer.ai forráson.

Ha saját lejátszókódot implementál, és integrálja a Cognitive Insights-widgetekkel, az Ön felelőssége, hogy ellenőrizze az üzenet forrását a VideoIndexer.ai.

### <a name="embed-widgets-in-your-app-or-blog-recommended"></a>Widgetek beágyazása az alkalmazásba vagy blogba (ajánlott)

Ez a szakasz azt mutatja be, hogyan lehet interakciót elérni két Video Indexer widget között, hogy amikor egy felhasználó kiválasztja az elemzés vezérlőt az alkalmazáson, a lejátszó a megfelelő pillanatra ugrik.

1. Másolja a Lejátszó vezérlő beágyazási kódját.
2. Másolja a Kognitív elemzési vezérlő beágyazási kódját.
3. Adja hozzá a [közvetítőfájlt](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js), amely a két vezérlő közötti kommunikációt kezeli:<br/> 
`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

Ha a felhasználó kiválasztja az alkalmazás elemzésvezérlőit, a lejátszó a megfelelő pillanatra ugrik.

További információkért lásd a Video Indexer [– A widgetek beágyazása bemutatót.](https://codepen.io/videoindexer/pen/NzJeOb)

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>A Kognitív elemzési vezérlő beágyazása és az Azure Media Player használata a tartalmak lejátszásához

Ez a szakasz bemutatja, hogyan lehet interakciót megvalósítani egy Cognitive Insights-widget és egy Azure Media Player-példány között az [AMP beépülő modul használatával.](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js)

1. Adjon hozzá Video Indexer az AMP-lejátszóhoz egy új beépülő modult:<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. Példányos Azure Media Player a Video Indexer beépülő modulban.

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

Most már kommunikálhat a Azure Media Player.

További információért tekintse meg a [Azure Media Player + VI Insights bemutatót.](https://codepen.io/videoindexer/pen/rYONrO)

### <a name="embed-the-video-indexer-cognitive-insights-widget-and-use-a-different-video-player"></a>Ágyazza be Video Indexer Cognitive Insights-vezérlőt, és használjon egy másik videólejátszót

Ha a videólejátszót nem Azure Media Player, manuálisan kell módosítania a videólejátszót a kommunikáció eléréséhez.

1. Szúrja be a videólejátszót.

    Például egy szabványos HTML5-lejátszó:

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

További információért tekintse meg a [Azure Media Player + VI Insights bemutatót.](https://codepen.io/videoindexer/pen/YEyPLd)

## <a name="adding-subtitles"></a>Feliratok hozzáadása

Ha saját Video Indexer ágyaz be elemzéseket, Azure Media Player metódussal feliratokat [](https://aka.ms/azuremediaplayer) `GetVttUrl` (feliratokat) kaphat. A JavaScript-metódust a Video Indexer AMP beépülő modulból is meg lehet hívni `getSubtitlesUrl` (ahogy korábban látható).

## <a name="customizing-embeddable-widgets"></a>Beágyazható vezérlők testreszabása

### <a name="cognitive-insights-widget"></a>Kognitív elemzési vezérlő

Kiválaszthatja a kívánt elemzéstípusokat. Ehhez adja meg őket értékként a következő URL-paraméterhez, amely a lekért beágyazási kódhoz lesz hozzáadva (az API-ból vagy a webalkalmazásból): `&widgets=<list of wanted widgets>` .

A lehetséges értékek: `people` , , , , , , , , `animatedCharacters` , , , , , `keywords` , és `labels` `sentiments` `emotions` `topics` `keyframes` `transcript` `ocr` `speakers` `scenes` `namedEntities` .

Ha például egy olyan vezérlőt szeretne beágyazni, amely csak személyeket és kulcsszavakkal kapcsolatos elemzéseket tartalmaz, az iframe beágyazási URL-címe a következő lesz:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords`

Az iframe ablak címe az `&title=<YourTitle>` iframe URL-címének megszabásával is testre szabható. (Ez szabja testre a `<title>` HTML-értéket).
   
Ha például az iframe-ablaknak a "MyInsights" címet szeretné adni, az URL-cím a következő lesz:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

Fontos tudni, hogy ez a beállítás csak olyan esetekben releváns, ha azt szeretné, hogy az elemzések új ablakban nyíljanak meg.

### <a name="player-widget"></a>Lejátszó vezérlő

A Video Indexer-lejátszó beágyazásakor megadhatja a lejátszó méretét az iframe méretének meghatározásával.

Például:

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

Alapértelmezés szerint Video Indexer lejátszó automatikusan létrehozott feliratokkal rendelkezik, amelyek a videó átiratán alapulnak. Az átirat a videóból lesz kinyerve a videó feltöltésekor kiválasztott forrásnyelvvel.

Ha más nyelven szeretne beágyazni, hozzáadhatja a címet a beágyazási `&captions=<Language Code>` lejátszó URL-címéhez. Ha azt szeretné, hogy a feliratok alapértelmezés szerint jelenek meg, adja meg a showCaptions=true &a következőt: .

A beágyazási URL-cím ekkor a következő lesz:

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=en-us`

#### <a name="autoplay"></a>Robotpilota

Alapértelmezés szerint a lejátszó elindítja a videó lejátszását. Dönthet úgy, hogy nem, ha az előző `&autoplay=false` beágyazási URL-címre adja át.

## <a name="code-samples"></a>Kódminták

Tekintse meg [az API-hoz](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Embedding%20widgets) és a widgethez Video Indexer mintákat tartalmazó kódminta-kódtárat:

| Fájl/mappa                       | Leírás                                |
|-----------------------------------|--------------------------------------------|
| `azure-media-player`              | Videóindexelő videó betöltése egyéni Azure Media Player.                        |
| `azure-media-player-vi-insights`  | A VI Insights beágyazása egyéni Azure Media Player.                             |
| `control-vi-embedded-player`      | Ágyazza be a VI Playert, és vezérelje kívülről.                                    |
| `custom-index-location`           | VI Insights beágyazása egyéni külső helyről (lehet egy ügyfél blobja).     |
| `embed-both-insights`             | A VI Insights alapszintű használata mind a lejátszóban, mind az elemzésekben.                            |
| `embed-insights-with-AMP`         | VI Insights-widget beágyazása egyéni Azure Media Player.                      |
| `customize-the-widgets`           | VI-widgetek beágyazása testreszabott beállításokkal.                                     |
| `embed-both-widgets`              | Ágyazza be a VI Playert és az Elemzéseket, és kommunikáljon közöttük.                      |
| `url-generator`                   | Egyéni beágyazási URL-címet hoz létre a widgetek számára a felhasználó által megadott beállítások alapján.             |
| `html5-player`                    | A VI Insights beágyazása egy alapértelmezett HTML5 videolejátszóval.                           |

## <a name="supported-browsers"></a>Támogatott böngészők

További információ: támogatott [böngészők.](video-indexer-overview.md#supported-browsers)

## <a name="next-steps"></a>Következő lépések

További információ az elemzések megtekintéséről és Video Indexer: View and edit Video Indexer insights (Elemzések [megtekintése Video Indexer szerkesztése).](video-indexer-view-edit.md)

Emellett tekintse meg a [Video indexer CodePent is.](https://codepen.io/videoindexer/pen/eGxebZ)
