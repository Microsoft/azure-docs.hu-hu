---
title: Azure Media Services dinamikus csomagolás áttekintése | Microsoft Docs
description: Ez a cikk áttekintést nyújt Microsoft Azure Media Services dinamikus csomagolásról.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 0358f7a6591309ad9ffa7263cdee417647a8fa2b
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106108166"
---
# <a name="dynamic-packaging"></a>Dinamikus csomagolás

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector" title1="Válassza ki a használt Media Services verzióját:"]
> * [3\. verzió](../latest/encode-dynamic-packaging-concept.md)
> * [2-es verzió](media-services-dynamic-packaging-overview.md)

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Tekintse meg a legújabb, [Media Services v3](../latest/index.yml)verziót. Lásd még: [az áttelepítési útmutató v2-től v3-ig](../latest/migrate-v-2-v-3-migration-introduction.md)

A Microsoft Azure Media Services számos különböző ügyfél-technológiára (például iOS, XBOX, Silverlight, Windows 8) képes a médiafájl-formátumok, a média-adatfolyam-formátumok és a tartalomvédelem formátumának továbbítására. Ezek az ügyfelek különböző protokollokat ismernek, például az iOS megköveteli a HTTP Live Streaming (HLS) v4 formátumot, és a Silverlight és az Xbox megköveteli a Smooth Streaming. Ha egy adaptív sávszélességű (többszörös sávszélességű) MP4 (ISO Base Media 14496-12) fájl vagy egy adaptív sávszélességű Smooth Streaming fájl van beállítva, amelyet az MPEG DASH, a HLS vagy a Smooth Streaming megértéséhez szeretne használni, használja ki Media Services dinamikus csomagolás előnyeit.

A dinamikus csomagolással minden esetben olyan eszközt kell létrehoznia, amely adaptív sávszélességű MP4-fájlokat vagy adaptív sávszélességű Smooth Streaming fájlokat tartalmaz. Ezután a manifest vagy a fragment kérelem megadott formátuma alapján az igény szerinti folyamatos átviteli kiszolgáló biztosítja, hogy megkapja az adatfolyamot a választott protokollon. Így elég egyetlen tárolási formátumban tárolni a fájlokat (és kifizetni a tárhelyüket), a Media Services szolgáltatás elkészíti és kiszolgálja az ügyféltől érkező kérésnek megfelelő választ.

A következő ábra a hagyományos kódolási és statikus csomagolási munkafolyamatot mutatja be.

![Statikus kódolás](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

A következő ábra a dinamikus csomagolási munkafolyamatot mutatja be.

![Dinamikus kódolás](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)

## <a name="common-scenario"></a>Gyakori forgatókönyv

1. Töltsön fel egy bemeneti fájlt (úgynevezett köztes fájl). Például: H. 264, MP4 vagy WMV (a támogatott formátumok listájának megtekintéséhez lásd [a Media Encoder standard által támogatott formátumokat](media-services-media-encoder-standard-formats.md).
2. Kódolja a köztes fájlt H. 264 MP4 adaptív sávszélességű készletbe.
3. Az adaptív sávszélességű MP4-készletet tartalmazó eszköz közzététele az igény szerinti lokátor létrehozásával.
4. Hozza létre a streaming URL-címeket a tartalom eléréséhez és továbbításához.

## <a name="preparing-assets-for-dynamic-streaming"></a>Eszközök előkészítése a dinamikus átvitelhez

Az eszköz dinamikus folyamatos átvitelre való előkészítéséhez a következő lehetőségek állnak rendelkezésre:

- [Töltsön fel egy főfájlt](media-services-dotnet-upload-files.md).
- [A Media Encoder standard kódoló használatával H. 264 MP4 adaptív sávszélesség-készleteket hozhat létre](media-services-dotnet-encode-with-media-encoder-standard.md).
- [Továbbítsa a tartalmat](media-services-deliver-content-overview.md).

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>Dinamikus csomagolás által támogatott hangkodekek

A dinamikus csomagolás támogatja az MP4-fájlokat, amelyek az [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) -kódolású hanganyagot (AAC-LC, it-AAC v1, it-AAC v2), a [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus)(Enhanced AC-3 vagy E-AC3), a Dolby Atmos vagy a [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) (DTS Express, DTS lbr, DTS HD, DTS HD Lossless) tartalmaznak. A Dolby Atmos-tartalmak folyamatos továbbítása támogatott olyan szabványok esetében, mint például az MPEG-DASH protokoll a Common Streaming Format (CSF) vagy a Common Media Application Format (CMAF) töredezett MP4-vel, valamint a (z) CMAF-vel rendelkező HTTP Live Streaming (HLS) használatával.

> [!NOTE]
> A dinamikus csomagolás nem támogatja a [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) hanganyagot tartalmazó fájlokat (ez egy örökölt kodek).

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
