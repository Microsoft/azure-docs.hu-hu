---
title: MPEG-DASH adaptív streaming-videó beágyazása egy HTML5-alkalmazásba DASH.jskal | Microsoft Docs
description: Ez a témakör bemutatja, hogyan ágyazhat be MPEG-DASH adaptív adatfolyam-videót egy HTML5-alkalmazásba DASH.js használatával.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 5aa0e7b6-f5c3-4cc1-aa33-ed16ea4780c2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 820c10a2f5bb43ff931027954b7ecf10c5b4c59f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103013567"
---
# <a name="embedding-an-mpeg-dash-adaptive-streaming-video-in-an-html5-application-with-dashjs"></a>MPEG-DASH adaptív streamelt videók beágyazása DASH.js-sel rendelkező HTML5-alkalmazásba

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

## <a name="overview"></a>Áttekintés
Az MPEG-DASH egy ISO-szabvány a videotartalom adaptív közvetítéséhez, amely jelentős előnyt nyújt a fejlesztőknek, akik kiváló minőségű, adaptív videó-adatfolyam-kimenetet kívánnak biztosítani. Az MPEG-DASH használatával a videó stream automatikusan átállítja az alacsonyabb definíciókat, amikor a hálózat túlzsúfolt lesz. Ez csökkenti annak a valószínűségét, hogy a néző "szüneteltetett" videót lát, miközben a lejátszó a következő néhány másodpercen belül letölti a lejátszást (vagyis csökkenti a pufferelés valószínűségét). A hálózati torlódások csökkentése esetén a videolejátszó egy magasabb színvonalú streamhez fog visszatérni. Ez a képesség a szükséges sávszélesség-szabályozáshoz is gyorsabb kezdési időt eredményez a videóhoz. Ez azt jelenti, hogy az első néhány másodpercet gyorsan le lehet tölteni a gyengébb minőségű szegmensben, majd magasabb színvonalú tartalmat kell megadnia a megfelelő tartalom pufferelése után.

A Dash.js a JavaScriptben írt nyílt forráskódú MPEG-DASH videolejátszó. Célja, hogy egy robusztus, platformfüggetlen lejátszót biztosítson, amely szabadon használható a videolejátszás szükséges alkalmazásokban. MPEG-DASH lejátszást biztosít bármely böngészőben, amely támogatja a W3C Media Source Extensions (MSE) szolgáltatást, amely jelenleg a Chrome, a Microsoft Edge és a IE11 (más böngészők az MSE támogatásának szándékát jelezték). A DASH.jsról a JS dash.js adattárában talál további információt.

## <a name="creating-a-browser-based-streaming-video-player"></a>Böngészőalapú streaming videolejátszó létrehozása
Egy olyan egyszerű weblap létrehozásához, amely a várt vezérlőkkel (lejátszás, szüneteltetés, visszatekerés stb.) jelenít meg egy videót, a következőket kell tennie:

1. HTML-oldal létrehozása
2. A videó címke hozzáadása
3. A dash.js-lejátszó hozzáadása
4. A lejátszó inicializálása
5. CSS-stílus hozzáadása
6. Az MSE-t implementáló böngészőben megtekintheti az eredményeket

A lejátszó inicializálása a JavaScript-kód néhány sorában végezhető el. A dash.js használatával tényleg egyszerűen beágyazható az MPEG-DASH videó a böngészőalapú alkalmazásokban.

## <a name="creating-the-html-page"></a>A HTML-oldal létrehozása
Első lépésként létre kell hoznia egy szabványos HTML-oldalt, amely tartalmazza a **videó** elemet, mentse a fájlt basicPlayer.html néven, ahogy az alábbi példa szemlélteti:

```html
    <!DOCTYPE html>
    <html>
      <head><title>Adaptive Streaming in HTML5</title></head>
      <body>
        <h1>Adaptive Streaming with HTML5</h1>
        <video id="videoplayer" controls></video>
      </body>
    </html>
```

## <a name="adding-the-dashjs-player"></a>A DASH.js-lejátszó hozzáadása
Ha hozzá szeretné adni a dash.js-hivatkozás megvalósítását az alkalmazáshoz, a dash.js Project legújabb verziójából kell megragadnia a dash.all.js fájlt. Ezt az alkalmazás JavaScript mappájába kell menteni. Ez a fájl egy olyan kényelmi fájl, amely az összes szükséges dash.js kódot egyetlen fájlba kéri össze. Ha megnézzük a dash.js adattárat, megtalálhatja az egyes fájlokat, tesztelheti a kódokat és még sok más, de ha az összeset szeretné használni, dash.js, akkor az dash.all.js fájlra van szüksége.

Ha hozzá szeretné adni az dash.js-lejátszót az alkalmazásaihoz, adjon hozzá egy script címkét a Head szakaszhoz basicPlayer.html:

```html
    <!-- DASH-AVC/265 reference implementation -->
    < script src="js/dash.all.js"></script>
```

Ezután hozzon létre egy függvényt a lejátszó inicializálásához, amikor az oldal betöltődik. Adja hozzá a következő szkriptet a dash.all.js betöltését követően:

```html
    <script>
    // setup the video element and attach it to the Dash player
    function setupVideo() {
      var url = "http://wams.edgesuite.net/media/MPTExpressionData02/BigBuckBunny_1080p24_IYUV_2ch.ism/manifest(format=mpd-time-csf)";
      var context = new Dash.di.DashContext();
      var player = new MediaPlayer(context);
                      player.startup();
                      player.attachView(document.querySelector("#videoplayer"));
                      player.attachSource(url);
    }
    </script>
```

Ez a függvény először létrehoz egy DashContext. Ez egy adott futtatókörnyezeti környezet alkalmazásának konfigurálására szolgál. Technikai szempontból meghatározza azokat az osztályokat, amelyeket a függőségi injektálási keretrendszernek használnia kell az alkalmazás összeállításakor. A legtöbb esetben a Dash. di. DashContext használja.

Ezután hozza létre a dash.js keretrendszer elsődleges osztályát, a Media Player. Ez az osztály tartalmazza a szükséges alapszintű módszereket, például a lejátszást és a szüneteltetést, felügyeli a kapcsolatot a videó elemmel, és kezeli a Media Presentation Description (MPD) fájl értelmezését is, amely leírja a lejátszani kívánt videót.

A Media Player osztály indítási () függvényének meghívásával biztosítható, hogy a lejátszó készen álljon a videó lejátszására. Többek között a függvény gondoskodik arról, hogy a szükséges osztályok (a kontextusban meghatározottak szerint) be legyenek töltve. Ha a lejátszó készen áll, a attachView () függvénnyel csatolhatja a videó elemet. Az indítási függvény lehetővé teszi, hogy a Media Player beinjektálja a videó streamet az elembe, és szükség szerint vezérelje a lejátszást.

Adja át az MPD-fájl URL-címét a Media Player, így az a várt videóról értesül. Az imént létrehozott setupVideo () függvényt akkor kell végrehajtani, ha a lap teljes betöltése megtörtént. Ezt a body elem onload eseményének használatával teheti meg. Módosítsa az `<body>` elemet a következőre:

```html
    <body onload="setupVideo()">
```

Végül állítsa be a videó elem méretét a CSS használatával. Az adaptív adatfolyam-környezetekben ez különösen fontos, mert a lejátszani kívánt videó mérete változhat a hálózati feltételek módosítására való váltás közben. Ebben az egyszerű bemutatóban egyszerűen kényszeríteni kell a videó elemet a rendelkezésre álló böngészőablak 80%-ában úgy, hogy hozzáadja a következő CSS-t az oldal Head szakaszához:

```html
    <style>
    video {
      width: 80%;
      height: 80%;
    }
    </style>
```

## <a name="playing-a-video"></a>Videó lejátszása
Videó lejátszásához irányítsa a böngészőt a basicPlayback.html fájlban, és kattintson a lejátszás gombra a megjelenő videolejátszó lapon.

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még:

[GitHub dash.js adattár](https://github.com/Dash-Industry-Forum/dash.js) 

