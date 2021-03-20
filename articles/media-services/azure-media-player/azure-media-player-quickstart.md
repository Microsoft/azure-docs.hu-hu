---
title: Azure Media Player rövid útmutató
description: Ismerje meg a Azure Media Player beállításának alapvető lépéseit.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: quickstart
ms.date: 04/20/2020
ms.openlocfilehash: 1634731381be6ebbb8750746f88b8ff9e761025d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98879190"
---
# <a name="azure-media-player-quickstart"></a>Az Azure Media Player gyorsútmutatója
A Azure Media Player egyszerűen beállítható. Csak néhány percet vesz igénybe, hogy lekérje a médiatartalom alapszintű lejátszását a Azure Media Services-fiókjából. Ez a szakasz nagy vonalakban ismerteti az alapvető lépéseket. Az alábbi szakaszokban a Azure Media Player beállításával és konfigurálásával kapcsolatos konkrét tudnivalókat talál.  Egyszerűen adja hozzá a következőket a dokumentum `<head>` eleméhez:

```html
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <script src= "//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
```

> [!IMPORTANT]
> A verziót **ne** használja `latest` éles környezetben, mert az igény szerint változhat. Cserélje le a `latest` (Azure Media Player) verzióját a helyére, például a következővel: `latest` `1.0.0` . Azure Media Player verziók lekérdezése [innen](azure-media-player-changelog.md)lehetséges.

## <a name="use-the-video-element"></a>A videó elem használata

Ezután egyszerűen használja az `<video>` elemet úgy, ahogy a szokásos módon tenné, de egy további, `data-setup` tetszőleges beállításokat tartalmazó attribútummal. Ezek a beállítások bármilyen Azure Media Services lehetőséget tartalmazhatnak egy érvényes JSON-objektumban.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" autoplay controls width="640" height="400" poster="poster.jpg" data-setup='{"nativeControlsForTouch": false}'>
        <source src="http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest" type="application/vnd.ms-sstr+xml" />
        <p class="amp-no-js">
            To view this video please enable JavaScript, and consider upgrading to a web browser that supports HTML5 video
        </p>
    </video>
```

Ha nem szeretné az automatikus telepítést használni, kihagyhatja az `data-setup` attribútumot, és manuálisan is inicializálhatja a videó elemet.

```html
    var myPlayer = amp('vid1', { /* Options */
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
            }
          }
    );
    myPlayer.src([{
        src: "http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
        type: "application/vnd.ms-sstr+xml"
    }]);
```

## <a name="next-steps"></a>Következő lépések ##

- [Azure Media Player teljes telepítés](./azure-media-player-full-setup.md)