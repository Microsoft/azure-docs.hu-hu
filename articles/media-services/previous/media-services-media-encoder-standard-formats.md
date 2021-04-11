---
title: Media Encoder Standard formátumok és kodekek – Azure
description: Ez a cikk áttekintést nyújt Media Encoder Standard formátumokról és kodekekről.
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
ms.reviewer: anilmur
ms.openlocfilehash: 9055c4c1af14c9ecc8fbdeb621c30ac6ebe5afc4
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106108805"
---
# <a name="media-encoder-standard-formats-and-codecs"></a>Media Encoder Standard-formátumok és -kodekek

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector" title1="Válassza ki a használt Media Services verzióját:"]
> * [2-es verzió](media-services-media-encoder-standard-formats.md)
> * [3\. verzió](../latest/encode-media-encoder-standard-formats-reference.md)

Ez a dokumentum a leggyakoribb importálási és exportálási fájlformátumok listáját tartalmazza, amelyeket Media Encoder Standard használhat.

## <a name="input-containerfile-formats"></a>Bemeneti tároló/fájlformátumok
| Fájlformátum (fájlkiterjesztés) | Támogatott |
| --- | --- |
| FLV (H.264 és AAC kodekkel) (.flv) |Yes |
| MXF (.mxf) |Yes |
| MXF (.mxf) |Yes |
| MPEG2 PS, MPEG2-TS, 3GP (.ts, .ps, .3gp, .3gpp, .mpg) |Yes |
| Windows Media Video (WMV) / ASP (.wmv, .asf) |Yes |
| AVI (8 bit-es/10 bit-es, tömörítettlen) (.avi) |Yes |
| MP4 (.mp4, .m4a, .m4v)/ISMV (.isma, .ismv) |Yes |
| [Microsoft Digital Video Recording(DVR-MS)](/previous-versions/windows/desktop/mstv/about-the-dvr-ms-file-format) (.dvr-ms) |Yes |
| Matroska/WebM (.mkv) |Yes |
| WAVE/WAV (.wav) |Yes |
| QuickTime (.mov) |Yes |

> [!NOTE]
> A fenti lista a leggyakrabban előforduló fájlkiterjesztéseket tartalmazza. A Media Encoder Standard számos más támogatást támogat (például:. m2ts,. mpeg2video,. Qt). Ha egy fájlt próbál kódolni, és a nem támogatott formátumra vonatkozó hibaüzenetet kap, [itt](https://feedback.azure.com/forums/169396-media-services/category/144411-encoding-and-processing/)adja meg a visszajelzését.
> 
> 

### <a name="audio-formats-in-input-containers"></a>Hangformátumok bemeneti tárolókban
Media Encoder Standard támogatja a következő hangformátumok átvitelét a bemeneti tárolókban:

* MXF-, GXF-és QuickTime-fájlok, amelyek hang-és hangsávokat tartalmazhatnak az összekapcsolt sztereó vagy 5,1-mintákkal

vagy

* Olyan MXF-, GXF- és QuickTime-fájlok, melyekben külön PCM sávban található a hang, de csatornaleképezés (sztereó vagy 5.1) a fájlok metaadataiból származtatható

## <a name="input-video-codecs"></a>Bemeneti videó codec-je
| Bemeneti videó codec-je | Támogatott |
| --- | --- |
| 8 bites/10 bites AVC, akár 4:2:2-esig, ideértve az AVCIntrát is |8 bites 4:2:0-es és 4:2:2-es |
| Avid DNxHD (MXF-ben) |Yes |
| DVCPro/DVCProHD (MXF-ben) |Yes |
| Digital video (DV) (AVI-fájlokban) |Yes |
| JPEG 2000 |Yes |
| MPEG-2 (akár a 422-es profilig és a magas szintig; az XDCAM, XDCAM HD, XDCAM IMX, CableLabs® és D10 típusú változatokat is beleértve) |Akár 422-es profilig |
| MPEG-1 |Yes |
| VC-1/WMV9 |Yes |
| Canopus HQ/HQX |No |
| MPEG-4 Part 2 |Yes |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Yes |
| Tömörítetlen YUV420 vagy mezzanine |Yes |
| Apple ProRes 422 |Yes |
| Apple ProRes 422 LT |Yes |
| Apple ProRes 422 HQ |Yes |
| Apple ProRes Proxy |Yes |
| Apple ProRes 4444 |Yes |
| Apple ProRes 4444 XQ |Yes |
| HEVC/H. 265| Fő és fő 10 (&#42;) profilok<br/>A 10 fő profil támogatása a 8bit 4:2:0-tartalomhoz készült. |

## <a name="input-audio-codecs"></a>Bemeneti hangkodekek
| Bemeneti hangkodekek | Támogatott |
| --- | --- |
| AAC (AAC-LC, AAC-HE és AAC-HEv2; akár 5.1-esig) |Yes |
| MPEG 2 réteg |Yes |
| MP3 (MPEG-1, 3 hangréteg) |Yes |
| Windows Media Audio |Yes |
| WAV/PCM |Yes |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Yes |
| [Opus](https://go.microsoft.com/fwlink/?LinkId=822667) |Yes |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Yes |
| AMR (adaptív többsebességű) |Yes |
| AES (SMPTE 331M és 302 M, AES3-2003) |No |
| Dolby® E |No |
| Dolby® Digital (AC3) |No |
| Dolby® Digital (E-AC3) |No |

## <a name="output-formats-and-codecs"></a>Kimeneti formátumok és kodekek
A következő táblázat az exportáláshoz támogatott kodekeket és fájlformátumokat sorolja fel.

| Fájlformátum | Videó kodek | Hangkodek |
| --- | --- | --- |
| MP4 <br/><br/>(többek között a többszörös sávszélességű MP4-tárolók) |H. 264 (magas, fő és alapkonfigurációs profilok) |AAC-LC, it-AAC v1, it-AAC v2 |
| MPEG2 – TS |H. 264 (magas, fő és alapkonfigurációs profilok) |AAC-LC, it-AAC v1, it-AAC v2 |

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még
[Igény szerinti tartalom kódolása Azure Media Services](media-services-encode-asset.md)

[Kódolás a Media Encoder Standard használatával](media-services-dotnet-encode-with-media-encoder-standard.md)
