---
title: Az Azure igény szerinti adathordozó-kódolóinak áttekintése | Microsoft Docs
description: Azure Media Services több lehetőséget kínál a felhőben lévő adathordozók kódolására. Ez a cikk áttekintést nyújt az Azure igény szerinti adathordozó-kódolóról.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 0fcf3aaa830c092dfa225f55fef882e2263b16cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103013550"
---
# <a name="overview-of-azure-on-demand-media-encoders"></a>Az Azure igény szerinti adathordozó-kódolóinak áttekintése

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Tekintse meg a legújabb, [Media Services v3](../latest/index.yml)verziót. Lásd még: [az áttelepítési útmutató v2-től v3-ig](../latest/migrate-v-2-v-3-migration-introduction.md)

Azure Media Services több lehetőséget kínál a felhőben lévő adathordozók kódolására.

A Media Services indításakor fontos megérteni a kodekek és a fájlformátumok közötti különbséget.
A kodekek a tömörítési/kibontási algoritmust megvalósító szoftverek, míg a fájlformátumok olyan tárolók, amelyek a tömörített videót tárolják.

A Media Services dinamikus csomagolást biztosít, amely lehetővé teszi az adaptív sávszélességű MP4-vagy Smooth Streaming-kódolású tartalom továbbítását a Media Services által támogatott streaming formátumokban (MPEG DASH, HLS, Smooth Streaming) anélkül, hogy újra kellene csomagolnia ezeket a streaming formátumokba.

A Media Services fiók létrehozásakor a rendszer **leállított** állapotban adja hozzá a fiókhoz az **alapértelmezett** folyamatos átviteli végpontot. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie. A folyamatos átviteli végpontok számlázása akkor történik meg, amikor a végpont **futó** állapotban van.

Media Services a következő igény szerinti kódolót támogatja:

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)

Ez a cikk rövid áttekintést nyújt az igény szerinti adathordozó-kódolókkal kapcsolatban, és részletesebb információkat tartalmazó cikkekre mutató hivatkozásokat tartalmaz.

Alapértelmezés szerint minden Media Services fiók egyszerre csak egy aktív kódolási feladattal rendelkezhet. Olyan kódolási egységeket foglalhat le, amelyek lehetővé teszik, hogy egyszerre több kódolási feladatot futtasson, egyet a megvásárolt kódoláshoz fenntartott egységekhez. További információ: [kódolási egységek skálázása](media-services-scale-media-processing-overview.md).

## <a name="media-encoder-standard"></a>Media Encoder Standard

### <a name="how-to-use"></a>Használat
[Kódolás a Media Encoder Standard használatával](media-services-dotnet-encode-with-media-encoder-standard.md)

### <a name="formats"></a>Formátumok
[Formátumok és kodekek](media-services-media-encoder-standard-formats.md)

### <a name="presets"></a>Készletek
Media Encoder Standard az [itt](./media-services-mes-presets-overview.md)ismertetett kódoló-beállításkészletek egyikével van konfigurálva.

### <a name="input-and-output-metadata"></a>Bemeneti és kimeneti metaadatok
A kódolók bemeneti metaadatait [itt](media-services-input-metadata-schema.md)mutatjuk be.

A kódoló kimeneti metaadatait [itt](media-services-output-metadata-schema.md)mutatjuk be.

### <a name="generate-thumbnails"></a>Miniatűrök létrehozása
További információ: [miniatűrök készítése a Media Encoder standard használatával](media-services-advanced-encoding-with-mes.md).

### <a name="trim-videos-clipping"></a>Videók körülvágása (vágás)
További információ: [videók körülvágása Media Encoder standard használatával](media-services-advanced-encoding-with-mes.md#trim_video).

### <a name="create-overlays"></a>Átfedések létrehozása
További információ: [átfedések létrehozása Media Encoder standard használatával](media-services-advanced-encoding-with-mes.md#overlay).

### <a name="see-also"></a>Lásd még
[A Media Services blog](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)

### <a name="known-issues"></a>Ismert problémák
Ha a bemeneti videó nem tartalmaz kódolt feliratozást, a kimeneti eszköz továbbra is tartalmaz egy üres TTML-fájlt.

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Kapcsolódó cikkek
* [Speciális kódolási feladatok végrehajtása a Media Encoder Standard-készletek testreszabásával](media-services-custom-mes-presets-with-dotnet.md)
* [Kvóták és korlátozások](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/