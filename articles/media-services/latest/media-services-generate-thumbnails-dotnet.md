---
title: Miniatűrök előállítása Media Encoder Standard .NET használatával
description: Ez a cikk bemutatja, hogyan lehet a .NET használatával kódolni egy objektumot, és hogyan hozhatja ki a miniatűrket Media Encoder Standard használatával.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 12/01/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 3efb5aa864f0940b7b5aa6a9131da9ce8bd732ee
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "101092813"
---
# <a name="how-to-generate-thumbnails-using-encoder-standard-with-net"></a>Miniatűrök készítése a .NET-es kódoló standard használatával

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

A Media Encoder Standard használatával [JPEG](https://en.wikipedia.org/wiki/JPEG) vagy [png](https://en.wikipedia.org/wiki/Portable_Network_Graphics) formátumú képfájlformátumban létrehozhat egy vagy több miniatűrt a bemeneti videóból.

## <a name="recommended-reading-and-practice"></a>Ajánlott olvasás és gyakorlat

Azt javasoljuk, hogy ismerkedjen meg az egyéni átalakításokkal, ha beolvassa a [kódolást egy egyéni átalakító-.net](customize-encoder-presets-how-to.md)használatával.

## <a name="transform-code-example"></a>Példa a kód átalakítására

Az alábbi kód példa csak egy miniatűrt hoz létre.  A következő paramétereket kell beállítania:

- **Start** – a bemeneti videó pozíciója, ahonnan megkezdheti a miniatűrök generálását. Az érték lehet ISO 8601 formátumú (például PT05S 5 másodpercnél), vagy a keretek száma (például 10 a tizedik keretnél), vagy egy relatív érték az adatfolyam időtartamára (például 10%, hogy a stream időtartamának 10%-ában kezdődjön). A egy {Best} nevű makrót is támogat, amely azt jelzi, hogy a kódoló kijelöli a legjobb miniatűrt a videó első néhány másodpercében, és csak egy miniatűrt hoz létre, függetlenül attól, hogy milyen egyéb beállítások tartoznak a lépéshez és a tartományhoz. Az alapértelmezett érték a Macro {Best}.
- **lépés** – a bélyegképek generálásának intervallumai. Az érték ISO 8601 formátumú lehet (például 5 másodpercenként egy kép PT05S), vagy a keretek száma (például 30 képkockával 30 keretnél), vagy relatív érték az adatfolyam időtartamára (például 10% az egyik rendszerképhez a stream időtartamának 10%-ában). A lépés értéke hatással lesz az első generált miniatűrre, ami nem feltétlenül egyezik meg a beállított indítási időpontnál megadott értékkel. Ennek oka a kódoló, amely az első kimenetként a kezdő időpont és a lépés pozíciója közötti legjobb miniatűrt próbálja kiválasztani. Mivel az alapértelmezett érték 10%, az azt jelenti, hogy ha az adatfolyam hosszú ideig tart, az első generált miniatűr a kezdési időponttól távol lehet. Próbáljon meg egy ésszerű értéket választani a lépéshez, ha az első miniatűr várhatóan a kezdési időponthoz közeledik, vagy állítsa a tartomány értékét 1-re, ha a kezdéskor csak egy miniatűrre van szükség.
- **Range (tartomány** ) – a bemeneti videóban az előre beállított indítási időponthoz viszonyított pozíció, amelyen a miniatűrök generálásának leállítása megszakítható. Az érték ISO 8601 formátumú lehet (például a PT5M30S 5 percen belül, 30 másodperccel a kezdési időponttól), vagy a keretek száma (például 300, hogy a keretből leállítsa a 300th keretet a kezdő időpontnál. Ha ez az érték 1, az azt jelenti, hogy csak egy miniatűrt állít elő a kezdési időpontnál, vagy egy relatív értéket az adatfolyam időtartamára (például 50%, hogy leállítsa a stream időtartamának felét a kezdési időponttól számítva). Az alapértelmezett érték 100%, ami azt jelenti, hogy a stream végén le kell állítani.
- **rétegek** – a kódoló által előállított kimeneti képrétegek gyűjteménye.

```csharp

private static Transform EnsureTransformExists(IAzureMediaServicesClient client, string resourceGroupName, string accountName, string transformName)
{
    // Does a Transform already exist with the desired name? Assume that an existing Transform with the desired name
    // also uses the same recipe or Preset for processing content.
    Transform transform = client.Transforms.Get(resourceGroupName, accountName, transformName);

    if (transform == null)
    {
        // Create a new Transform Outputs array - this defines the set of outputs for the Transform
        TransformOutput[] outputs = new TransformOutput[]
        {
            // Create a new TransformOutput with a custom Standard Encoder Preset
            // This demonstrates how to create custom codec and layer output settings

          new TransformOutput(
                new StandardEncoderPreset(
                    codecs: new Codec[]
                    {
                        // Generate a set of PNG thumbnails
                        new PngImage(
                            start: "25%",
                            step: "25%",
                            range: "80%",
                            layers: new PngLayer[]{
                                new PngLayer(
                                    width: "50%",
                                    height: "50%"
                                )
                            }
                        )
                    },
                    // Specify the format for the output files for the thumbnails
                    formats: new Format[]
                    {
                        new PngFormat(
                            filenamePattern:"Thumbnail-{Basename}-{Index}{Extension}"
                        )
                    }
                ),
                onError: OnErrorType.StopProcessingJob,
                relativePriority: Priority.Normal
            )
        };

        string description = "A transform that includes thumbnails.";
        // Create the custom Transform with the outputs defined above
        transform = client.Transforms.CreateOrUpdate(resourceGroupName, accountName, transformName, outputs, description);
    }

    return transform;
}
```

## <a name="next-steps"></a>Következő lépések

[Miniatűrök előállítása REST használatával](media-services-generate-thumbnails-rest.md)
