---
title: Azure Media Player API-metódusok
description: A Azure Media Player API lehetővé teszi, hogy a JavaScript használatával kommunikáljon a videóval, hogy a böngésző a HTML5 videó, a Flash, a Silverlight vagy bármely más támogatott lejátszási technológián keresztül játssza-e a videót.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: d1ad0cbe30ce20e61a31f0534a47fe031f6f257b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "81727267"
---
# <a name="api"></a>API #

A Azure Media Player API lehetővé teszi, hogy a JavaScript használatával kommunikáljon a videóval, hogy a böngésző a HTML5 videó, a Flash, a Silverlight vagy bármely más támogatott lejátszási technológián keresztül játssza-e a videót.

## <a name="referencing-the-player"></a>Hivatkozás a lejátszóra ##

Az API functions használatához hozzáféréssel kell rendelkeznie a Player objektumhoz. Szerencsére egyszerűen lekérhető. Csak meg kell győződnie arról, hogy a videó címkéje rendelkezik AZONOSÍTÓval. A példában szereplő beágyazási kódnak van egy azonosítója `vid1` . Ha egy oldalon több videó található, győződjön meg róla, hogy minden videó címkéhez egyedi azonosító tartozik.

`var myPlayer = amp('vid1');`

> [!NOTE]
> Ha a lejátszó még nem lett inicializálva az adattelepítési attribútummal vagy más módszerrel, a rendszer a lejátszót is inicializálja.

## <a name="wait-until-the-player-is-ready"></a>Várjon, amíg a lejátszó készen áll ##

A videó és az API beállításához szükséges Azure Media Player idő a használt lejátszási technológiától függően változhat. A HTML5 gyakran sokkal gyorsabban töltődik be, mint a Flash vagy a Silverlight. Emiatt a lejátszó "Ready" függvényét kell használni a játékos API-ját igénylő kódok elindításához.

```javacript
    amp("vid_1").ready(function(){
      var myPlayer = this;

      // EXAMPLE: Start playing the video.
      myPlayer.play();
    });
```

OR

```javacript
    var myPlayer = amp("vid_1", myOptions, function(){
        //this is the ready function and will only execute after the player is loaded
    });
```

## <a name="api-methods"></a>API-metódusok ##

Most, hogy hozzáfér egy kész lejátszóhoz, megadhatja a videót, beolvashatja az értékeket, vagy reagálhat a videó eseményeire. A Azure Media Player API-függvények nevei megpróbálják követni a [HTML5 Media API](http://www.whatwg.org/specs/web-apps/current-work/multipage/the-video-element.html)-t. A fő különbség az, hogy a videó tulajdonságaihoz a beolvasó/beállító függvények használatosak.

```javacript
    // setting a property on a bare HTML5 video element
    myVideoElement.currentTime = 120;

    // setting a property with Azure Media Player
    myPlayer.currentTime(120);
```

## <a name="registering-for-events"></a>Regisztrálás eseményekhez ##
Az eseményeket közvetlenül a lejátszó inicializálását követően kell regisztrálni, hogy az összes eseményt megfelelően jelenteni lehessen az alkalmazásnak, és a kész eseményen kívül kell történnie.

```javacript
    var myPlayer = amp("vid_1", myOptions, function(){
        //this is the ready function and will only execute after the player is loaded
    });
    myPlayer.addEventListener(amp.eventName.error, _ampEventHandler);
    //add other event listeners
```

## <a name="next-steps"></a>Következő lépések ##

<!---Some context for the following links goes here--->
- [Azure Media Player rövid útmutató](azure-media-player-quickstart.md)