---
title: Azure Media Player lejátszási technológia
description: További információ a videó vagy Hang lejátszásához használt lejátszási technológiáról.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.custom: devx-track-js
ms.openlocfilehash: 24a38fde3d1b6bbe1efef9241458da846c186112
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97916337"
---
# <a name="playback-technology-tech"></a>Lejátszási technológia ("Tech") #

A lejátszási technológia a videó vagy Hang lejátszásához használt konkrét böngészőre vagy plugin-technológiára utal.

- **azureHtml5JS**: az MSE és az eme szabványokat használja a bővítmények nélküli, a Dash-tartalomra épülő, az AES-128 bites, titkosított tartalommal vagy DRM-vel közös titkosított tartalommal (a PlayReady és a Widevine-n keresztül, ha a böngésző támogatja Azure Media Services)
- **flashs**: a Flash Player technológiáját használja a zökkenőmentes tartalom lejátszásához, amely támogatja az AES-128 bites boríték-visszafejtést Azure Media Services – a 11,4-es vagy újabb Flash-verziót igényli
- **html5FairPlayHLS**: a HLS és a videó elem használatával a Safari-specifikus böngésző-alapú lejátszási technológián keresztül használható. Ez a tech szükséges a FairPlay által védett tartalom lejátszásához Azure Media Servicesről, és a techOrder a 10/19/16
- **silverlights**: a Silverlight technológiáját használja a zökkenőmentes tartalom lejátszásához, és támogatja a Azure Media Services PlayReady védett tartalmakat.
- **HTML5**: böngésző alapú lejátszási technológiát használ a videó elemmel.  Egy Apple iOS-vagy Android-eszközön ez a technológia lehetővé teszi a HLS streamek lejátszását az AES-128 bites boríték-titkosítás vagy a DRM-tartalom (FairPlay-en keresztül, ha a böngésző támogatja) alapvető támogatásával.

## <a name="tech-order"></a>Technikai sorrend ##

Annak biztosítása érdekében, hogy az eszköz a legkülönbözőbb eszközökön legyen lejátszható, a következő technikai sorrend ajánlott, és az alapértelmezett, ha: `techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"]` , és a beállításokban közvetlenül a vagy a programozott módon is beállítható `<video>` :

`<video data-setup='{"techOrder": ["azureHtml5JS", "flashSS", "html5FairPlayHLS, "silverlightSS", "html5"]}`

vagy

```javascript
    amp("vid1", {
          techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS, "silverlightSS", "html5"]
    });
```

## <a name="compatibility-matrix"></a>Kompatibilitási mátrix ##

Az ajánlott technikai sorrend az Azure Media Services-ból származó tartalom továbbítása esetén az alábbi kompatibilitási lejátszási mátrix várható

| Böngésző        | Operációs rendszer                                                       | Várt technikai (tiszta)  | Várt technológia (AES)  | Várt technológia (DRM)          |
|----------------|----------------------------------------------------------|------------------------|----------------------|------------------------------|
| 11. szegély      | Windows 10, Windows 8,1, Windows Phone-telefon 10<sup>1</sup>               | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (PlayReady)     |
| IE 11          | Windows 7, Windows Vista<sup>1</sup>                     | vakuk                | vakuk              | Silverlight (PlayReady)    |
| IE 11          | Windows Phone 8.1                                        | azureHtml5JS           | azureHtml5JS         | nem támogatott                |
| Edge           | Xbox One<sup>1</sup> (november 2015 frissítés)                   | azureHtml5JS           | azureHtml5JS         | nem támogatott                |
| Chrome 37 +     | Windows 10, Windows 8,1, macOS X Yosemite<sup>1</sup>   | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Firefox 47 +    | Windows 10, Windows 8,1, macOS X Yosemite +<sup>1</sup>  | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Firefox 42-46  | Windows 10, Windows 8,1, macOS X Yosemite +<sup>1</sup>  | azureHtml5JS           | azureHtml5JS         | Silverlight (PlayReady)    |
| Firefox 35-41  | Windows 10, Windows 8,1                                  | vakuk                | vakuk              | Silverlight (PlayReady)    |
| Safari         | Legalább iOS 6                                                   | HTML5                  | HTML5 (nincs token) 3    | nem támogatott                |
| Safari 8 +      | OS X Yosemite +                                           | azureHtml5JS           | azureHtml5JS         | html5FairPlayHLS (FairPlay)  |
| 6. szafari       | OS X hegyi oroszlán<sup>1</sup>                           | vakuk                | vakuk              | Silverlight (PlayReady)    |
| Chrome 37 +     | Android 4.4.4 +<sup>2</sup>                               | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Chrome 37 +     | Android 4,02                                             | HTML5                  | HTML5 (nincs token)<sup>3</sup>    | nem támogatott                |
| Firefox 42 +    | Android 5.0 +<sup>2</sup>                                 | azureHtml5JS           | azureHtml5JS         | nem támogatott                |
| IE 8, IE 9, IE 10  | Windows                                                  | nem támogatott          | nem támogatott        | nem támogatott                |

<sup>1</sup> a konfiguráció nem támogatott vagy nem tesztelt; a kiegészítésre hivatkozásként szerepel.

<sup>2</sup> az androidos eszközökön való sikeres lejátszáshoz az eszköz képességeinek, a grafikus támogatásnak, a kodek-renderelésnek, az operációs rendszer támogatásának és egyebeknek az együttes Mivel az Android egy nyílt forráskódú platform, amely lehetővé teszi a telefonos gyártók számára, hogy megváltoztassák a Google által biztosított vanília Android operációs rendszert, ami némi töredezettséget okoz az Android-térben, és bizonyos eszközök nem támogatottak a szolgáltatások hiánya miatt. Emellett egyes Android-eszközök nem támogatják az összes kodeket.  

<sup>3</sup> azokban az esetekben, amelyekben a jogkivonat nem támogatott, a funkció hozzáadására proxy használható. Tekintse meg ezt a [blogot](https://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/) , ha többet szeretne megtudni erről a megoldásról.

> [!NOTE]
> Ha a várt technikai beállításhoz egy beépülő modult kell telepíteni, például a Flash-t, amely nincs telepítve a felhasználó számítógépén, az AMP továbbra is a következő Tech képességeit fogja ellenőriznie a forrás típusaival és a védelmi információkkal együtt a tech listáról. Ha például egy nem védett, igény szerinti adatfolyamot próbál meg megtekinteni a Safari 8-as verziójában az OS X Yosemite-on, és a Flash és a Silverlight is nincs telepítve, az AMP a natív Html5 Tech for lejátszást fogja kiválasztani.<br/><br/>Az új böngésző-technológiák naponta jelentkeznek, és ez hatással lehet erre a mátrixra.

## <a name="next-steps"></a>Következő lépések ##

- [Azure Media Player rövid útmutató](azure-media-player-quickstart.md)