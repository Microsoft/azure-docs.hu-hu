---
title: Media Services által ajánlott élő adatfolyam-kódolók
description: Ismerkedjen meg a Media Services által ajánlott élő streaming helyszíni kódolókkal
services: media-services
keywords: kódolás; kódolók; adathordozó
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: media-services
ms.openlocfilehash: bf282b3fbba0f22cd41c0420014c46ddb7c958eb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98955139"
---
# <a name="verified-on-premises-live-streaming-encoders"></a>Ellenőrzött helyszíni élő adatfolyam-kódolók

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services egy [élő esemény](/rest/api/media/liveevents) (csatorna) az élő közvetítésre szánt tartalom feldolgozásához szükséges folyamatot jelöli. Az élő esemény két módon fogadja az élő bemeneti adatfolyamokat.

* A helyszíni élő kódoló egy többszörös sávszélességű RTMP vagy Smooth Streaming (darabolt MP4) streamet küld az élő eseményre, amely nincs engedélyezve az élő kódolás végrehajtásához Media Services. A betöltött adatfolyamok további feldolgozás nélkül haladnak át az élő eseményeken. Ezt a metódust **áteresztőnek** nevezzük. Azt javasoljuk, hogy az élő kódoló többszörös átviteli sebességű streameket küldjön egyetlen sávszélességű adatfolyamként egy átmenő élő esemény helyett, hogy az adaptív sávszélességű adatfolyamot továbbítsa az ügyfélnek. 

    Ha több sávszélességű adatfolyamot használ az átmenő élő eseményhez, szinkronizálni kell a video GOP-méretet és a különböző bitráták videós darabjait, hogy elkerülje a nem várt viselkedést a lejátszási oldalon.

  > [!TIP]
  > Az átmenő módszer használata a leggazdaságosabb módja az élő közvetítésnek.
 
* A helyszíni élő kódoló egyetlen sávszélességű streamet küld az élő eseménynek, amely lehetővé teszi, hogy az Media Services az alábbi formátumok valamelyikével végezzen élő kódolást: RTMP vagy Smooth Streaming (töredezett MP4). Az élő esemény ezután a bejövő egyszeri átviteli sebességű adatfolyam élő kódolását egy többszörös sávszélességű (adaptív) videó streamre hajtja végre.

Ez a cikk a ellenőrzött helyszíni élő adatfolyam-kódolókat ismerteti. Az ellenőrzés a szállítói önellenőrzés vagy vásárlói adatok ellenőrzése használatával történik. Microsoft Azure Media Services nem hajtja végre az egyes kódolók teljes vagy szigorú tesztelését, és nem ellenőrzi folyamatosan újra a frissítéseket. A helyszíni élő kódoló ellenőrzésével kapcsolatos útmutatásért lásd [a helyszíni kódoló ellenőrzése](become-on-premises-encoder-partner.md) című témakört.

A Media Services élő kódolásával kapcsolatos részletes információkért lásd: [élő adatfolyamok Media Services v3](live-streaming-overview.md).

## <a name="encoder-requirements"></a>Kódolóval kapcsolatos követelmények

A kódolók csak HTTPS-vagy RTMP-protokollok használata esetén támogatják a TLS 1,2-et.

## <a name="live-encoders-that-output-rtmp"></a>Az RTMP kimenetét futtató élő kódolók

A Media Services a következő, RTMP kimenetű élő kódolók használatát javasolja. A támogatott URL-sémák a `rtmp://` vagy a `rtmps://` .

Amikor RTMP-vel streamel, ellenőrizze a tűzfal és/vagy a proxy beállításaiban, hogy az 1935-ös és az 1936-os kimenő TCP-portok nyitva vannak-e.<br/><br/>
Amikor RTMPS-sel streamel, ellenőrizze a tűzfal és/vagy a proxy beállításaiban, hogy a 2935-ös és a 2936-os kimenő TCP-portok nyitva vannak-e.

> [!NOTE]
> Az RTMP protokollok használata esetén a kódolóknak támogatniuk kell a TLS 1,2-et.

- Adobe Flash Media Live Encoder 3.2
- [Antix digitális](http://www.antixdigital.com/) StreamZ Live (korábban Imagine Communication SelenioFlex Live)
- [Blackmagic ATEM mini és ATEM mini PRO](https://www.blackmagicdesign.com/products/atemmini)
- [Cambria élő 4,3](https://www.capellasystems.net/products/cambria-live/)
- Elemi élő (2.14.15 és újabb verzió)
- [FFmpeg](https://www.ffmpeg.org)
- [GoPro](https://gopro.com/help/articles/block/getting-started-with-live-streaming) 7. hős és Hero 8
- Haivision KB
- Haivision Makito X HEVC
- [Restream.io](https://restream.io/)
- OBS Studio
- [Streamlabs OBS](https://streamlabs.com/)
- [Switcher Studio (iOS)](https://www.switcherstudio.com/)
- Wirecast (13.0.2 vagy újabb verzió) a TLS 1,2-követelmény miatt
- Wirecast (csak RTMP támogatott). A TLS 1.2-es és újabb verzióinak hiánya miatt nem támogatott az RTMP-támogatás
- Teradek Slice 756
- VMIX
- xStream

> [!WARNING]
> A kódolók fenti listája csak egy javaslati lista. A kódolókat a Microsoft nem teszteli vagy érvényesíti folyamatosan, és a frissítésekkel vagy a változtatásokkal kapcsolatos változásokat a kódoló szállítók vagy olyan nyílt forráskódú projektek is bevezethetik, amelyek képesek a kompatibilitás megszakadására. 

## <a name="live-encoders-that-output-fragmented-mp4-smooth-streaming-ingest"></a>Feldarabolt MP4 kimenetet (Smooth Streaming betöltést) támogató élő kódolók

Media Services javasolja a következő élő kódolók egyikének használatát, amelyekben a többszörös sávszélességű Smooth Streaming (töredékes MP4) kimenetként van használatban. A támogatott URL-sémák a `http://` vagy a `https://` .

> [!NOTE]
> HTTPS protokollok használata esetén a kódolóknak támogatniuk kell a TLS 1,2-et.

- Ateme TITAN Live
- [Antix digitális](http://www.antixdigital.com/) StreamZ Live (korábban Imagine Communication SelenioFlex Live)
- Cisco Digital Media Encoder 2200
- Elemi élő (a TLS 1,2-követelmény miatti 2.14.15 és újabb verzió)
- Envivio 4Caster C4 Gen III 
- [FFmpeg](https://www.ffmpeg.org)
- Media Excel Hero Live és Hero 4K (UHD/HEVC)

> [!TIP]
>  Ha több nyelven végez élő eseményeket (például egy angol hangsávot és egy spanyol hangsávot), akkor ezt a Media Excel Live encoderben állíthatja be úgy, hogy az élő hírcsatornát továbbítsa egy átmenő élő eseményre.

> [!WARNING]
> A kódolók fenti listája csak egy javaslati lista. A kódolókat a Microsoft nem teszteli vagy érvényesíti folyamatosan, és a kódoló gyártói vagy olyan nyílt forráskódú projektek is bemutatják, amelyek bármikor megszakítják a kompatibilitást. 

## <a name="configuring-on-premises-live-encoder-settings"></a>Helyszíni élő kódoló beállításainak konfigurálása

Arról, hogy milyen beállítások érvényesek az élő esemény típusára, lásd: [élő események típusai összehasonlítás](live-event-types-comparison.md).

### <a name="playback-requirements"></a>Lejátszási követelmények

A tartalom lejátszásához a hang-és video streamnek is jelen kell lennie. A csak videó stream lejátszása nem támogatott.

### <a name="configuration-tips"></a>Konfigurációs tippek

- Lehetőség szerint használjon vezetékes internetkapcsolatot.
- A sávszélesség-követelmények meghatározásakor a továbbítási bitrátát dupla értékre kell kiszámítani. Bár nem kötelező, ez az egyszerű szabály segít csökkenteni a hálózati torlódás hatását.
- A szoftveres alapú kódolók használatakor zárjunk be minden szükségtelen programot.
- A kódoló konfigurációjának a megkezdése után történő módosítása negatív hatással van az eseményre. A konfigurációs változások hatására az esemény instabillá válhat. 
- Mindig tesztelje és érvényesítse a kódoló szoftver újabb verzióit a Azure Media Services való folyamatos kompatibilitás érdekében. A Microsoft nem ellenőrzi újra a kódolókat ezen a listán, és a legtöbb érvényesítést a szoftvergyártók közvetlenül "öntanúsítás" néven hajtják végre.
- Győződjön meg arról, hogy elegendő időt ad az esemény beállítására. A nagy léptékű események esetében javasoljuk, hogy a telepítőt egy órával az esemény előtt indítsa el.
- Használja a H. 264 videót és az AAC-LC audio codec kimenetét.
- Tartsa meg a támogatott felbontásokat és a képkockasebességet az élő esemény típusaként (például 60fps jelenleg Elutasítva).
- Győződjön meg arról, hogy a videó tulajdonságai között van-e kulcsfontosságú keret vagy GOP időbeli igazítás.
- Győződjön meg arról, hogy minden videó minőségének egyedi az adatfolyam neve.
- Az optimális adaptív sávszélességű teljesítmény érdekében ajánlott a szigorú CBR-kódolás használata.

> [!IMPORTANT]
> Tekintse meg a gép fizikai állapotát (CPU/memória/etc) a felhőbe való feltöltéshez, amely processzor-és IO-műveleteket is tartalmaz. Ha módosítja a kódoló bármelyik beállítását, állítsa vissza a csatorna/élő esemény alaphelyzetbe állítását, hogy a módosítás érvénybe lépjen.

## <a name="see-also"></a>Lásd még

[Élő közvetítés a Media Services v3-val](live-streaming-overview.md)

## <a name="next-steps"></a>Következő lépések

[A kódoló ellenőrzése](become-on-premises-encoder-partner.md)
