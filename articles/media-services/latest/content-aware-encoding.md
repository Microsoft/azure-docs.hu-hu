---
title: A Content-Aware kódoláshoz beállított beállításkészlet
description: Ez a cikk az Microsoft Azure Media Services v3 tartalommal kompatibilis kódolását ismerteti.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: ce18e71ced320c408933caeb39b469d5885bd6ba
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101095931"
---
# <a name="use-the-content-aware-encoding-preset-to-find-the-optimal-bitrate-value-for-a-given-resolution"></a>Az adott megoldás optimális bitráta-értékének megkereséséhez használja a Content-Aware kódolási beállításkészletet

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

A tartalom [adaptív sávszélességű adatfolyamként](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)történő továbbításához a videót több átviteli sebességre kell kódolni (magasról alacsonyra). Ez biztosítja a minőség kedvező romlását, mivel a bitrátát csökkenti, így a videó felbontása is megtörténik. Az ilyen több átviteli sebességű kódolás egy úgynevezett kódolási létrát használ, amely a felbontások és a bitráták táblázatát tartalmazza, a Media Services [beépített kódolási előkészleteket](/rest/api/media/transforms/createorupdate#encodernamedpreset).

Vegye figyelembe, hogy milyen tartalmat dolgoz fel, és testreszabhatja/hangolhatja a kódolási létrát az egyes videók összetettsége érdekében. Minden egyes megoldásnál van egy bitráta, amelyen kívül a minőség növekedése nem érzékelhető – a kódoló ebben az optimális bitráta-értékben működik. A következő optimalizálási szint a tartalomon alapuló felbontások kiválasztására szolgál, például egy PowerPoint-bemutató videója nem éri el a 720p-t. Továbbra is a kódoló feladata, hogy optimalizálja a videón belüli egyes lövések beállításait. 

A Microsoft [adaptív streaming](autogen-bitrate-ladder.md) -készlete részben a forrás videók minőségének és megoldásának változékonyságával foglalkozik. Ügyfeleink többféle tartalommal rendelkeznek, néhányat 1080p-ra, másokat pedig 720p-ra, és néhányat SD és alacsonyabb felbontásban. Továbbá, nem minden tartalomforrás kiváló minőségű, film-vagy TV-stúdiókból származó. Az adaptív adatfolyam-készlet ezeket a problémákat úgy oldja meg, hogy a bitráta-létrán soha nem haladja meg a bemeneti időpontok felbontását vagy az átlagos bitrátát. Ez az előre definiált beállítás azonban nem vizsgálja meg a forrás tulajdonságait a felbontás és a bitráta helyett.

## <a name="the-content-aware-encoding"></a>A Content-Aware kódolás

A Content-Aware kódolási beállításkészlet kiterjeszti az "adaptív sávszélességű adatfolyam" mechanizmust olyan egyéni logika beépítésével, amely lehetővé teszi, hogy a kódoló egy adott megoldás optimális sebességének megadását, de nem igényel kiterjedt számítási elemzést. Ez a beállításkészlet GOP-igazítású MP4 állít elő. A szolgáltatás a bemeneti tartalom kezdeti könnyű elemzését, valamint az eredmények használatával határozza meg a rétegek optimális számát, a megfelelő bitrátát és a megoldási beállításokat az adaptív adatfolyam-továbbítással. Ez a beállításkészlet különösen alacsony és közepes komplexitású videók esetében érvényes, ahol a kimeneti fájlok alacsonyabb sávszélességű lesznek, mint az adaptív adatfolyam-készlet, de olyan minőségben, amely továbbra is jó élményt nyújt a nézők számára. A kimenet video-és hangalapú MP4-fájlokat fog tartalmazni

A következő minta-diagramok az összehasonlítást a minőségi mérőszámok, például a [PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio) és a [VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion)használatával mutatják be. A forrást úgy hozták létre, hogy összefűzött néhány rövid klipet a nagy komplexitású felvételektől a filmek és a TÉVÉMŰSORok alapján, hogy kihangsúlyozzák a kódolót. A definíció szerint ez a készlet olyan eredményeket hoz létre, amelyek a tartalomtól a tartalomtól függően változnak – ez azt is jelenti, hogy egyes tartalmak esetében nem lehet jelentős mértékben csökkenteni a bitrátát vagy javítani a minőséget.

![A torzítás (RD) görbéje a PSNR használatával](media/content-aware-encoding/msrv1.png)

**1. ábra: a ráta-torzítás (RD) görbe használata a PSNR metrikával a nagy komplexitású forráshoz**

![A torzítás (RD) görbéje a VMAF használatával](media/content-aware-encoding/msrv2.png)

**2. ábra: a ráta-torzítás (RD) görbe használata a VMAF metrikával a nagy komplexitású forráshoz**

Az alábbiakban láthatók a tartalomforrás egy másik kategóriájára vonatkozó eredmények, ahol a kódoló meghatározta, hogy a bemenet gyenge minőségű-e (az alacsony sávszélesség miatt sok tömörítési összetevő). Vegye figyelembe, hogy a "Content-Aware" készlettel a kódoló úgy döntött, hogy csak egy kimeneti réteget hoz létre – egy elég alacsony bitráta mellett, így a legtöbb ügyfél nem fog tudni lejátszani az adatfolyamot.

![Távoli asztali görbe a PSNR használatával](media/content-aware-encoding/msrv3.png)

**3. ábra: távoli asztali görbe a PSNR használatával alacsony színvonalú bevitelhez (1080p)**

![Távoli asztali görbe a VMAF használatával](media/content-aware-encoding/msrv4.png)

**4. ábra: távoli asztali görbe a VMAF használatával alacsony színvonalú bevitelhez (1080p)**

## <a name="how-to-use-the-content-aware-encoding-preset"></a>A Content-Aware kódolási beállításkészlet használata 

A következő módon hozhat létre olyan átalakításokat, amelyek ezt az beállításkészletet használják. 

Az átalakítási kimeneteket használó oktatóanyagokat a [következő lépések](#next-steps) szakaszban találja. A kimeneti eszköz az olyan protokollok Media Services streaming végpontokból is elérhető, mint például az MPEG-DASH és a HLS (az oktatóanyagokban látható).

> [!NOTE]
> Ügyeljen arra, hogy az **ContentAwareEncoding** előre beállított ContentAwareEncodingExperimental ne használja.

```csharp
TransformOutput[] output = new TransformOutput[]
{
   new TransformOutput
   {
      // The preset for the Transform is set to one of Media Services built-in sample presets.
      // You can customize the encoding settings by changing this to use "StandardEncoderPreset" class.
      Preset = new BuiltInStandardEncoderPreset()
      {
         // This sample uses the new preset for content-aware encoding
         PresetName = EncoderNamedPreset.ContentAwareEncoding
      }
   }
};
```

> [!NOTE]
> Az `ContentAwareEncoding` előre beállított kódolási feladatok számlázása a kimeneti percek alapján történik. 
  
## <a name="next-steps"></a>Következő lépések

* [Oktatóanyag: videók feltöltése, kódolása és továbbítása a Media Services v3 segítségével](stream-files-tutorial-with-api.md)
* [Oktatóanyag: Távoli fájl kódolása URL-cím alapján és videó streamelése – REST](stream-files-tutorial-with-rest.md)
* [Oktatóanyag: távoli fájl kódolása URL-cím alapján és a videó továbbítása – parancssori felület](stream-files-cli-quickstart.md)
* [Oktatóanyag: távoli fájl kódolása URL-cím alapján és stream a video-.NET](stream-files-dotnet-quickstart.md)
* [Oktatóanyag: távoli fájl kódolása URL-cím alapján és stream a videón – Node.js](stream-files-nodejs-quickstart.md)
