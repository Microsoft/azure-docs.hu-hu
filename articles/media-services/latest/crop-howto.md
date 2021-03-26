---
title: Videofájlok körülvágása a Media Services-.NET használatával | Microsoft Docs
description: A vágás a videó keretén belül egy téglalap alakú ablak kiválasztásának, valamint az adott ablakon belüli képpontok kódolásának a folyamata. Ez a témakör bemutatja, hogyan lehet a videofájlokat a .NET használatával Media Services.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/24/2021
ms.author: inhenkel
ms.openlocfilehash: 43d0e1e3b8c0eaa37a3b09557b333c3abafe8b63
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105572374"
---
# <a name="how-to-crop-video-files-with-media-services---net"></a>Videofájlok körülvágása Media Services-.NET használatával

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

A Media Services használatával elvégezheti a bemeneti videó levágását. A vágás a videó keretén belül egy téglalap alakú ablak kiválasztásának, valamint az adott ablakon belüli képpontok kódolásának a folyamata. Az alábbi ábrán a folyamat szemlélteti.

## <a name="pre-processing-stage"></a>Előkészítés előtti szakasz

A vágás egy előfeldolgozási szakasz, így a kódolási beállításkészletben lévő *levágási paraméterek* a *bemeneti* videóra érvényesek. A kódolás egy későbbi fázis, és a szélességi/magassági beállítások az *előre feldolgozott* videóra vonatkoznak, és nem az eredeti videóra. Az előre beállított beállítások megtervezéséhez tegye a következőket:

1. Válassza ki a vágási paramétereket az eredeti bemeneti videó alapján
1. A bevágott videó alapján válassza ki a kódolás beállításait.

> [!WARNING]
> Ha nem felel meg a bevágott videóhoz tartozó kódolási beállításoknak, a kimenet nem a várt módon fog megjelenni.

Például a bemeneti videó felbontása 1920 × 1080 képpontos (16:9 oldalarányú), de a bal és a jobb oldali fekete sávokkal (oszlopokkal) rendelkezik, így csak a 4:3-es vagy a 1440x1080 képpont tartalmaz aktív videót. A fekete sávokat levágja, és kódolhatja a 1440x1080-területeket.

## <a name="transform-code"></a>Kód átalakítása

A következő kódrészlet bemutatja, hogyan írhat egy átalakítót a .NET-ben a videók körülvágásához.  A kód azt feltételezi, hogy rendelkezik egy helyi fájllal a szolgáltatással való együttműködéshez.

- A bal oldalon a termés bal szélső helye látható.
- A Top a termés legfelső szintű helye.
- A szélesség a termés végső szélessége.
- A magasság a termés végső magassága.

```dotnet
var preset = new StandardEncoderPreset

    {

        Filters = new Filters

        {                   

            Crop = new Rectangle

            {

                Left = "200",

                Top = "200",

                Width = "1280",

                Height = "720"

            }

        },

        Codecs =

        {

            new AacAudio(),

            new H264Video()

            {

                Layers =

                {                           

                    new H264Layer

                    {

                        Bitrate = 1000000,

                        Width = "1280",

                        Height = "720"

                    }

                }

            }

        },

        Formats =

        {

            new Mp4Format

            {

                FilenamePattern = "{Basename}_{Bitrate}{Extension}"

            }

        }

    }

```
