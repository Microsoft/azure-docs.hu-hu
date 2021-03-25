---
title: Smooth Streaming beépülő modul a nyílt forráskódú adathordozó-keretrendszerhez
description: Ismerje meg, hogyan használhatja a Azure Media Services Smooth Streaming beépülő modult az Adobe nyílt forráskódú adathordozó-keretrendszerhez.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 6068151f-b6b0-4507-9346-f03416d3d572
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: b0969ffae7963d6a5f9ac249e43750444ab11baa
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105036050"
---
# <a name="how-to-use-the-microsoft-smooth-streaming-plugin-for-the-adobe-open-source-media-framework"></a>A Microsoft Smooth Streaming beépülő modul használata az Adobe nyílt forráskódú adathordozó-keretrendszerhez

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Áttekintés
A nyílt forráskódú adathordozó-keretrendszer 2,0-es (OSMF)-es Microsoft Smooth Streaming beépülő modulja kibővíti a OSMF alapértelmezett képességeit, és hozzáadja a Microsoft Smooth Streaming a tartalom lejátszását az új és a meglévő OSMF-játékosokhoz. A beépülő modul emellett Smooth Streaming lejátszási képességeket is biztosít a Strobe Media lejátszásához (SMP).

A OSMF SS a beépülő modul két verzióját tartalmazza:

* Statikus Smooth Streaming beépülő modul a OSMF (. SWC)
* Dinamikus Smooth Streaming beépülő modul a OSMF (. swf)

Ez a dokumentum azt feltételezi, hogy az olvasó a OSMF és a OSMF beépülő modulokkal kapcsolatos általános ismeretekkel rendelkezik. A OSMF kapcsolatos további információkért tekintse meg a [hivatalos OSMF webhelyen](http://osmf.org/)található dokumentációt.

### <a name="smooth-streaming-plugin-for-osmf-20"></a>Smooth Streaming beépülő modul a OSMF 2,0-hez
A beépülő modul az igény szerinti Smooth Streaming tartalmak betöltését és lejátszását támogatja a következő funkciókkal:

* Igény szerinti Smooth Streaming lejátszás (lejátszás, szüneteltetés, keresés, leállítás)
* Élő Smooth Streaming lejátszás (lejátszás)
* Élő DVR-függvények (szüneteltetés, keresés, DVR lejátszás, élő indítás)
* Videós kodekek támogatása – H. 264
* Audio codec-támogatás – AAC
* Több hangos nyelvi váltás a OSMF beépített API-kkal
* Maximális lejátszási minőség kiválasztása a OSMF beépített API-kkal
* Oldalkocsis kódolt feliratok a OSMF Captions beépülő modullal
* Adobe &reg; Flash &reg; Player 11,4 vagy újabb verzió.
* Ez a verzió csak a 2,0-es OSMF támogatja.

## <a name="supported-features-and-known-issues"></a>Támogatott funkciók és ismert problémák
A támogatott szolgáltatások, a nem támogatott funkciók és az ismert problémák teljes listáját [ebben a dokumentumban](https://azure.microsoft.com/blog/microsoft-adaptive-streaming-plugin-for-osmf-update/)tekintheti meg.

## <a name="loading-the-plugin"></a>A beépülő modul betöltése
A OSMF beépülő modulok statikusan tölthetők be (fordítási időben) vagy dinamikusan (futásidőben). A OSMF letöltésére szolgáló Smooth Streaming beépülő modul dinamikus és statikus verziókat is tartalmaz.

* Statikus betöltés: a statikus betöltéshez statikus függvénytár-(SWC-) fájl szükséges. A statikus beépülő modulok a projektekre mutató hivatkozásként lesznek hozzáadva, és a végső kimeneti fájlban egyesítve kerülnek a fordítási idő alá.
* Dinamikus betöltés: Ha dinamikusan szeretné betölteni a terhelést, szükség van egy előfordított (SWF) fájlra. A dinamikus beépülő modulok a futtatókörnyezetben vannak betöltve, és nem szerepelnek a projekt kimenetében. (Lefordított kimenet) A dinamikus beépülő modulok a HTTP és a FILE protokollok használatával tölthetők be.

A statikus és a dinamikus betöltéssel kapcsolatos további információkért tekintse meg a hivatalos [OSMF beépülő modul oldalát](https://www.ibm.com/support/knowledgecenter/SSLTBW_2.3.0/com.ibm.zos.v2r3.izua300/IZUHPINFO_PluginsPlanning.htm).

### <a name="ss-for-osmf-static-loading"></a>SS OSMF statikus betöltéshez
Az alábbi kódrészlet azt mutatja be, hogyan tölthetők be a OSMF SS beépülő modulja statikusan, és hogyan játszhat egy alapszintű videót a OSMF MediaFactory osztály használatával. Mielőtt belefoglalja az SS-t a OSMF-kódra, győződjön meg arról, hogy a projekt referenciája tartalmazza a "MSAdaptiveStreamingPlugin-v 1.0.3-OSMF 2.0. SWC" statikus beépülő modult.

```csharp
package 
{

    import com.microsoft.azure.media.AdaptiveStreamingPluginInfo;

    import flash.display.*;
    import org.osmf.media.*;
    import org.osmf.containers.MediaContainer;
    import org.osmf.events.MediaErrorEvent;
    import org.osmf.events.MediaFactoryEvent;
    import org.osmf.events.MediaPlayerStateChangeEvent;
    import org.osmf.layout.*;



    [SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
    public class TestPlayer extends Sprite
    {        
        public var _container:MediaContainer;
        public var _mediaFactory:DefaultMediaFactory;
        private var _mediaPlayerSprite:MediaPlayerSprite;


        public function TestPlayer( )
        {
            stage.quality = StageQuality.HIGH;

            initMediaPlayer();

        }

        private function initMediaPlayer():void
        {

            // Create the container (sprite) for managing display and layout
            _mediaPlayerSprite = new MediaPlayerSprite();    
            _mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
            _mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);
            _mediaPlayerSprite.scaleMode = ScaleMode.NONE;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            //Adds the container to the stage
            addChild(_mediaPlayerSprite);

            // Create a mediafactory instance
            _mediaFactory = new DefaultMediaFactory();

            // Add the listeners for PLUGIN_LOADING
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );

            // Load the plugin class 
            loadAdaptiveStreamingPlugin( );  

        }

        private function loadAdaptiveStreamingPlugin( ):void
        {
            var pluginResource:MediaResourceBase;

            pluginResource = new PluginInfoResource(new AdaptiveStreamingPluginInfo( )); 
            _mediaFactory.loadPlugin( pluginResource ); 
        }

        private function onPluginLoaded( event:MediaFactoryEvent ):void
        {
            // The plugin is loaded successfully.
            // Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.
        loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")

        }

        private function onPluginLoadFailed( event:MediaFactoryEvent ):void
        {
            // The plugin is failed to load ...
        }


        private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
        {
            var state:String;

            state =  event.state;

            switch (state)
            {
                case MediaPlayerState.LOADING: 

                    // A new source is started to load.

                    break;

                case  MediaPlayerState.READY :   
                    // Add code to deal with Player Ready when it is hit the first load after a source is loaded. 

                    break;

                case MediaPlayerState.BUFFERING :

                    break;

                case  MediaPlayerState.PAUSED :
                    break;      
                // other states ...          
            }
        }

        private function onPlayerFailed(event:MediaErrorEvent) : void
        {
            // Media Player is failed .           
        }

        private function loadMediaSource(sourceURL : String):void 
        {
            // Take an URL of SmoothStreamingSource's manifest and add it to the page.

            var resource:URLResource= new URLResource( sourceURL );

            var element:MediaElement = _mediaFactory.createMediaElement( resource );
            _mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;

            // Add the media element
            _mediaPlayerSprite.media = element;
        }     

    }
}
```


### <a name="ss-for-osmf-dynamic-loading"></a>SS a OSMF dinamikus betöltéséhez
Az alábbi kódrészlet bemutatja, hogyan tölthető be dinamikusan a OSMF SS beépülő modulja, és hogyan játszhat alapszintű videót a OSMF MediaFactory osztály használatával. Mielőtt belefoglalja az SS-t a OSMF-kódra, másolja a "MSAdaptiveStreamingPlugin-v 1.0.3-OSMF 2.0. swf" dinamikus beépülő modult a Project mappába, ha a fájlt a FILE Protocol használatával szeretné betölteni, vagy egy webkiszolgálón keresztül másolja a HTTP-terheléshez. A projekt hivatkozásaiban nincs szükség a "MSAdaptiveStreamingPlugin-v 1.0.3-OSMF 2.0. SWC" kifejezésre.

```csharp
package 
{

    import flash.display.*;
    import org.osmf.media.*;
    import org.osmf.containers.MediaContainer;
    import org.osmf.events.MediaErrorEvent;
    import org.osmf.events.MediaFactoryEvent;
    import org.osmf.events.MediaPlayerStateChangeEvent;
    import org.osmf.layout.*;
    import flash.events.Event;
    import flash.system.Capabilities;


    //Sets the size of the SWF

    [SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
    public class TestPlayer extends Sprite
    {        
        public var _container:MediaContainer;
        public var _mediaFactory:DefaultMediaFactory;
        private var _mediaPlayerSprite:MediaPlayerSprite;


        public function TestPlayer( )
        {
            stage.quality = StageQuality.HIGH;
            initMediaPlayer();
        }

        private function initMediaPlayer():void
        {

            // Create the container (sprite) for managing display and layout
            _mediaPlayerSprite = new MediaPlayerSprite();    
            _mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
            _mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);

            //Adds the container to the stage
            addChild(_mediaPlayerSprite);

            // Create a mediafactory instance
            _mediaFactory = new DefaultMediaFactory();

            // Add the listeners for PLUGIN_LOADING
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );

            // Load the plugin class 
            loadAdaptiveStreamingPlugin( );  

        }

        private function loadAdaptiveStreamingPlugin( ):void
        {
            var pluginResource:MediaResourceBase;
            var adaptiveStreamingPluginUrl:String;

            // Your dynamic plugin web server needs to host a valid crossdomain.xml file to allow loading plugins.

            adaptiveStreamingPluginUrl = "http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf";
            pluginResource = new URLResource(adaptiveStreamingPluginUrl);
            _mediaFactory.loadPlugin( pluginResource ); 

        }

        private function onPluginLoaded( event:MediaFactoryEvent ):void
        {
            // The plugin is loaded successfully.

            // Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.

    loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")
        }

        private function onPluginLoadFailed( event:MediaFactoryEvent ):void
        {
            // The plugin is failed to load ...
        }


        private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
        {
            var state:String;

            state =  event.state;

            switch (state)
            {
                case MediaPlayerState.LOADING: 

                    // A new source is started to load.

                    break;

                case  MediaPlayerState.READY :   
                    // Add code to deal with Player Ready when it is hit the first load after a source is loaded. 

                    break;

                case MediaPlayerState.BUFFERING :

                    break;

                case  MediaPlayerState.PAUSED :
                    break;      
                // other states ...          
            }
        }

        private function onPlayerFailed(event:MediaErrorEvent) : void
        {
            // Media Player is failed .           
        }

        private function loadMediaSource(sourceURL : String):void 
        {
            // Take an URL of SmoothStreamingSource's manifest and add it to the page.

            var resource:URLResource= new URLResource( sourceURL );

            var element:MediaElement = _mediaFactory.createMediaElement( resource );
            _mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            // Add the media element
            _mediaPlayerSprite.media = element;
        }     

    }
}
```

## <a name="strobe-media--playback-with-the-ss-odmf-dynamic-plugin"></a>Strobe Media lejátszás az SS ODMF dinamikus beépülő modullal
A OSMF dinamikus beépülő moduljának Smooth Streaming kompatibilis a [Strobe Media lejátszással (SMP)](https://sourceforge.net/adobe/smp/home/Strobe%20Media%20Playback/). Az SS for OSMF beépülő modullal felveheti Smooth Streaming tartalom lejátszását az SMP-be. Ehhez másolja a "MSAdaptiveStreamingPlugin-v 1.0.3-OSMF 2.0. swf" nevű webkiszolgálót a HTTP-terheléshez a következő lépésekkel:

1. Tallózással keresse meg a [Strobe Media visszajátszás beállítása lapot](http://osmf.org/dev/2.0gm/setup.html). 
2. Az src beállítása Smooth Streaming forrásra (például http: \/ /devplatem.vo.msecnd.net/Sintel/Sintel_H264.ISM/manifest) 
3. Végezze el a kívánt konfigurációs módosításokat, és kattintson az előnézet és frissítés elemre.
   
   **Megjegyzés** A tartalmi webkiszolgálónak érvényes crossdomain.xmlra van szüksége. 
4. Másolja és illessze be a kódot egy egyszerű HTML-lapra a kedvenc szövegszerkesztő használatával, például a következő példában:

    ```html
    <html>
    <body>
    <object width="920" height="640"> 
    <param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
    <param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest &autoPlay=true"></param>
    <param name="allowFullScreen" value="true"></param>
    <param name="allowscriptaccess" value="always"></param>
    <param name="wmode" value="direct"></param>
    <embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
        type="application/x-shockwave-flash" 
        allowscriptaccess="always" 
        allowfullscreen="true" 
        wmode="direct" 
        width="920" 
        height="640" 
        flashvars=" src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true">
    </embed>
    </object>
    </body>
    </html>
    ```


1. Adja hozzá Smooth Streaming OSMF beépülő modult a beágyazási kódhoz, és mentse.
   
    ```html
    <html>
    <object width="920" height="640"> 
    <param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
    <param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10"></param>
    <param name="allowFullScreen" value="true"></param>
    <param name="allowscriptaccess" value="always"></param>
    <param name="wmode" value="direct"></param>
    <embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
        type="application/x-shockwave-flash" 
        allowscriptaccess="always" 
        allowfullscreen="true" 
        wmode="direct" 
        width="920" 
        height="640" 
        flashvars="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10">
    </embed>
    </object>
    </html>
    ```

2. Mentse a HTML-oldalt, és tegye közzé a webkiszolgálón. Tallózással keresse meg a közzétett weblapot a kedvenc Flash &reg; Player-kompatibilis böngésző használatával (Internet Explorer, Chrome, Firefox stb.).
3. Az Adobe Flash Player Smooth Streaming tartalmait használhatja &reg; &reg; .

Az általános OSMF-fejlesztéssel kapcsolatos további információkért tekintse meg a hivatalos [OSMF-fejlesztési oldalt](http://osmf.org/resources.html).

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még:
[Microsoft adaptív streaming beépülő modul a OSMF frissítéséhez](https://azure.microsoft.com/blog/2014/10/27/microsoft-adaptive-streaming-plugin-for-osmf-update/) 

