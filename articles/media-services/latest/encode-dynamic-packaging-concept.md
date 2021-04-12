---
title: Dinamikus csomagolás a Azure Media Services v3-ban
description: Ez a cikk áttekintést nyújt a Azure Media Services dinamikus csomagolásáról.
author: myoungerman
manager: femila
services: media-services
ms.service: media-services
ms.workload: media
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: inhenkel
ms.openlocfilehash: c2ffda7400109e16cf1110a4e14ecbc3604a7ecf
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106492307"
---
# <a name="dynamic-packaging-in-media-services-v3"></a>Dinamikus csomagolás a Media Services v3-ban

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

A Microsoft Azure Media Services számos médiafájl-formátum kódolására használható. A szolgáltatás különböző streaming protokollokon keresztül érhető el, tartalom védelme nélkül vagy anélkül, hogy elérje az összes jelentős eszközt (például az iOS és az Android rendszerű eszközöket). Ezek az ügyfelek megismerhetik a különböző protokollokat. Az iOS esetében például HTTP Live Streaming (HLS) formátumban kell továbbítani a streameket, és az Android-eszközök támogatják a HLS, valamint az MPEG DASH-t.

Media Services a streaming- [végpont](stream-streaming-endpoint-concept.md) (forrás) egy dinamikus (igény szerinti) csomagolási és forrás-szolgáltatást jelent, amely az élő és igény szerinti tartalmat közvetlenül az ügyfél-lejátszó alkalmazásnak kézbesíti. A következő szakaszban említett Common Streaming Media-protokollok egyikét használja. A *dinamikus csomagolás* egy olyan szolgáltatás, amely minden folyamatos átviteli végponton szabványos.

## <a name="to-prepare-your-source-files-for-delivery"></a>A forrásfájlok előkészítése a kézbesítéshez

A dinamikus csomagolás kihasználása érdekében a közbenső (forrás) fájlt több bitrátás MP4-(ISO Base Media 14496-12) fájlba kell [kódolnia](encode-concept.md) . Media Services dinamikus csomagoláshoz szükséges, kódolt MP4-és folyamatos átviteli konfigurációs fájlokkal rendelkező [eszközre](assets-concept.md) van szükség. Ebből az MP4-fájlokból a dinamikus csomagolás használatával továbbíthatja a videókat az alább ismertetett Streaming Media-protokollok segítségével.

Azure Media Services a dinamikus csomagolás csak az MP4-tároló formátumában támogatja a videó-és hangfájlokat. A hangfájlokat MP4-tárolóba kell kódolni, valamint alternatív kodekek, például a Dolby használata esetén.  

> [!TIP]
> Az MP4 és a streaming konfigurációs fájlok beszerzésének egyik módja, ha a [köztes fájlt a Media Services használatával kódolja](#encode-to-adaptive-bitrate-mp4s). 

Ahhoz, hogy a kódolt eszközön a videók elérhetők legyenek az ügyfelek számára a lejátszáshoz, létre kell hoznia egy [folyamatos átviteli lokátort](stream-streaming-locators-concept.md) és a streaming URL-címeket. Ezután a streaming Client manifest (HLS, MPEG DASH vagy Smooth Streaming) megadott formátuma alapján megkapja az adatfolyamot a választott protokollon.

Így elég egyetlen tárolási formátumban tárolni a fájlokat (és kifizetni a tárhelyüket), a Media Services szolgáltatás elkészíti és kiszolgálja az ügyféltől érkező kérésnek megfelelő választ.

Ha a tartalom Media Services dinamikus titkosítással való védelemmel való ellátását tervezi, tekintse meg a [streaming protokollok és a titkosítási típusok](drm-content-protection-concept.md#streaming-protocols-and-encryption-types)című témakört.

### <a name="hls-protocol"></a>HLS protokoll

A streaming-ügyfél a következő HLS-formátumokat adhatja meg:

|Protokoll|Példa|
|---|---|
|HLS V4 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl)`|
|HLS V3 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl-v3)`|
|HLS CMAF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-cmaf)`|

> [!NOTE]
> Az Apple korábbi irányelvei azt javasolták, hogy az alacsony sávszélességű hálózatok tartaléka csak hangalapú streamet adjon meg.  Jelenleg a Media Services kódoló automatikusan létrehoz egy csak hangot.  Az Apple-irányelvek mostantól azt az állapotot jelentik, hogy a csak hangvezérelt nyomon követés *nem* vehető fel, különösen az Apple TV-disztribúcióhoz.  Ha meg szeretné akadályozni, hogy a lejátszó ne legyen alapértelmezett a csak hangvezéreltként, javasoljuk, hogy az URL-címben a "csak hang = hamis" címkét használja, amely eltávolítja a HLS, vagy egyszerűen csak a HLS-v3-t használja. Például: `http://host/locator/asset.ism/manifest(format=m3u8-aapl,audio-only=false)`.

### <a name="mpeg-dash-protocol"></a>MPEG-DASH protokoll

A streaming-ügyfél a következő MPEG-DASH formátumokat adhatja meg:

|Protokoll|Példa|
|---|---|
|MPEG-DASH CSF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-csf)` |
|MPEG-DASH CMAF|`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-cmaf)` |

### <a name="smooth-streaming-protocol"></a>Smooth Streaming protokoll

A folyamatos átviteli ügyfél a következő Smooth Streaming formátumokat adhatja meg:

|Protokoll|Megjegyzések/példák| 
|---|---|
|Smooth Streaming| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest`|
|Smooth Streaming 2,0 (örökölt jegyzékfájl)|Alapértelmezés szerint Smooth Streaming jegyzékfájl formátuma az ismétlés címkét (r-tag) tartalmazza. Bizonyos játékosok azonban nem támogatják a használatát `r-tag` . Az ezekkel a játékosokkal rendelkező ügyfelek használhatnak olyan formátumot, amely letiltja az r-címkét:<br/><br/>`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=fmp4-v20)`|

> [!NOTE]
> Smooth Streaming megköveteli, hogy a hang és a videó legyen jelen a streamben.

## <a name="on-demand-streaming-workflow"></a>Igény szerinti folyamatos átviteli munkafolyamat

Az alábbi lépések egy közös Media Services streaming-munkafolyamatot mutatnak be, amelyben a dinamikus csomagolás a Azure Media Services standard kódolóval együtt használható.

1. [Töltsön fel egy bemeneti fájlt](job-input-from-http-how-to.md) , például MP4, QuickTime/MOV vagy más támogatott fájlformátumot. Ezt a fájlt a köztes vagy a forrásfájl is nevezik. A támogatott formátumok listáját lásd: [a standard kódoló által támogatott formátumok](encode-media-encoder-standard-formats-reference.md).
1. A köztes fájl [kódolása](#encode-to-adaptive-bitrate-mp4s) egy H. 264/AAC MP4 adaptív sávszélesség-készletbe.

    Ha már rendelkezik kódolt fájlokkal, és csak a fájlok másolását és továbbítását szeretné használni, használja a következőt: [CopyVideo](/rest/api/media/transforms/createorupdate#copyvideo) és [CopyAudio](/rest/api/media/transforms/createorupdate#copyaudio) API-k. Ennek eredményeképpen létrejön egy adatfolyam-jegyzékfájlt (. ISM fájlt) tartalmazó új MP4-fájl.
1. Tegye közzé az adaptív sávszélességű MP4-készletet tartalmazó kimeneti eszközt. Egy [adatfolyam-kereső](stream-streaming-locators-concept.md)létrehozásával teheti közzé.
1. Különböző formátumokat (HLS, MPEG-DASH és Smooth Streaming) célzó URL-címeket hozhat létre. A *folyamatos átviteli végpont* gondoskodik a megfelelő jegyzékfájl és kérések kiszolgálásáról a különböző formátumokhoz.
    
A következő ábra az igény szerinti folyamatos átvitelt mutatja be dinamikus csomagolási munkafolyamattal.

![Az igény szerinti folyamatos átvitelre szolgáló munkafolyamat ábrája dinamikus csomagolással](./media/encode-dynamic-packaging-concept/media-services-dynamic-packaging.svg)

A letöltési útvonal a fenti képen látható, hogy az MP4-fájlok közvetlenül a *streaming végponton* (forrás) keresztül tölthetők le (az adatfolyam-keresőben a letölthető [adatfolyam-szabályzatot](stream-streaming-policy-concept.md) kell megadnia).<br/>A dinamikus csomagoló nem módosítja a fájlt. Igény szerint az Azure Blob Storage API-kkal közvetlenül is elérheti az MP4-t, ha szeretné megkerülni az *adatfolyam-végpont* (forrás) funkcióit. 

### <a name="encode-to-adaptive-bitrate-mp4s"></a>Kódolás adaptív sávszélességű MP4

A következő cikkek példákat mutatnak [be a videók Media Services használatával történő kódolására](encode-concept.md):

* [KÓDOLÁS HTTPS-URL-címről beépített beállításkészletek használatával](job-input-from-http-how-to.md).
* [Helyi fájl kódolása beépített beállításkészletek használatával](job-input-from-local-file-how-to.md).
* [Hozzon létre egy egyéni beállításkészletet az adott forgatókönyv vagy eszköz követelményeinek megcélzásához](transform-custom-presets-how-to.md).

Tekintse meg a szabványos kódoló [-formátumok és-kodekek](encode-media-encoder-standard-formats-reference.md)listáját.

## <a name="live-streaming-workflow"></a>Élő adatfolyam-továbbítási munkafolyamat

Egy élő esemény lehet egy *átmenő* (egy helyszíni élő kódoló több bitrátás streamet küld) vagy *élő kódolást* (a helyszíni élő kódoló egyetlen sávszélességű adatfolyamot küld). 

Az alábbi gyakori munkafolyamat a *dinamikus csomagolással* folytatott élő közvetítéshez használható:

1. Hozzon létre egy [élő eseményt](live-event-outputs-concept.md).
1. Szerezze be a betöltési URL-címet, és konfigurálja a helyszíni kódolót úgy, hogy az URL-cím használatával küldje el a hozzájárulási csatornát.
1. Szerezze be az előnézeti URL-címet, és annak ellenőrzéséhez, hogy a rendszer beolvassa-e a kódolótól érkező adatokat.
1. Hozzon létre egy új eszközt.
1. Hozzon létre egy élő kimenetet, és használja a létrehozott eszköz nevét.<br />Az élő kimenet archiválja a streamet az eszközre.
1. Hozzon létre egy streaming-keresőt a beépített folyamatos átviteli házirend-típusokkal.<br />Ha titkosítani szeretné a tartalmakat, tekintse át a [tartalomvédelem áttekintését](drm-content-protection-concept.md).
1. Listázza a streaming-lokátor elérési útját a használandó URL-címek lekéréséhez.
1. Szerezze be annak a streaming-végpontnak az állomásnevét, amelyről streamet szeretne továbbítani.
1. Különböző formátumokat (HLS, MPEG-DASH és Smooth Streaming) célzó URL-címeket hozhat létre. A *folyamatos átviteli végpont* gondoskodik a megfelelő jegyzékfájl és kérések kiszolgálásáról a különböző formátumokban.

Ez az ábra az élő adatfolyamok *dinamikus csomagolással* való működésének munkafolyamatát mutatja be:

![A dinamikus csomagolással rendelkező, átmenő kódolásra szolgáló munkafolyamat ábrája](./media/live-streaming/pass-through.svg)

További információ a Media Services v3 élő közvetítéséről: [élő közvetítés – áttekintés](stream-live-streaming-concept.md).

## <a name="video-codecs-supported-by-dynamic-packaging"></a>Dinamikus csomagolás által támogatott videó-kodekek

A dinamikus csomagolás támogatja az MP4-tároló fájlformátumában lévő videofájlokat, és a [h. 264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC) (MPEG-4 AVC vagy AVC1) és [h. 265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding) (HEVC, hev1 vagy hvc1) kódolású videókat tartalmaz.

> [!NOTE]
> Akár 4K-ig terjedő, akár 60-es képkockákból álló és a frame sebességű, *dinamikus csomagolással* tesztelt felbontások is megoldhatók.

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>Dinamikus csomagolás által támogatott hangkodekek

A dinamikus csomagolás a következő kodekek egyikében is támogatja a kódolt hangadatfolyamot tartalmazó MP4 file Container formátumban tárolt hangfájlokat:

* [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, it-AAC v1 vagy it-AAC v2). 
* [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (bővített AC-3 vagy E-AC3).  A kódolt hangot MP4-tároló formátumban kell tárolni, hogy dinamikus csomagolással működjön.
* Dolby Atmos

   A folyamatos átviteli Dolby Atmos-tartalom olyan szabványok esetében támogatott, mint például az MPEG-DASH protokoll a Common Streaming Format (CSF) vagy a Common Media Application Format (CMAF) darabolt MP4-vel, valamint a (z) HTTP Live Streaming (HLS) és a CMAF használatával.
* [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29)<br />
   A DASH-CSF, DASH-CMAF, HLS-M2TS és HLS-CMAF csomagolási formátumok által támogatott DTS-kodekek a következők:  

    * DTS digitális surround (dtsc)
    * DTS-HD nagy felbontású és DTS-HD Master hang (dtsh)
    * DTS Express (dtse)
    * DTS-HD veszteségmentes (nincs mag) (DTSL)

A dinamikus csomagolás több hangsávot is támogat a DASH vagy a HLS (4-es vagy újabb verzió) esetében, így több, több kodekkel és nyelvtel rendelkező hangsávot tartalmazó adatfolyam-továbbítási eszköz is használható.

A fenti hangkodekek esetében a kódolt hangot az MP4-tároló formátumában kell tárolni, hogy a dinamikus csomagolás működjön. A szolgáltatás nem támogatja a nyers elemes adatfolyam-fájlformátumokat a blob Storage-ban (például a következők nem támogatottak:. DTS,. AC3.) 

A hangcsomagoláshoz csak a. mp4 kiterjesztésű fájlok támogatottak. 

### <a name="limitations"></a>Korlátozások

#### <a name="ios-limitation-on-aac-51-audio"></a>iOS-korlátozás az AAC 5,1 hangon

Az Apple iOS-eszközök nem támogatják a 5,1 AAC hangkodeket. A többcsatornás hangot a Dolby Digital vagy a Dolby Digital Plus codec használatával kell kódolni.

Részletes információkat az Apple- [eszközök HLS-létrehozási specifikációja](https://developer.apple.com/documentation/http_live_streaming/hls_authoring_specification_for_apple_devices)című témakörben talál.

> [!NOTE]
> A Media Services nem támogatja a Dolby Digital, a Dolby Digital Plus vagy a Dolby Digital Plus, valamint a Dolby Atmos többcsatornás hangformátumok kódolását.

#### <a name="dolby-digital-audio"></a>Dolby Digitális hang

Media Services a dinamikus csomagolás jelenleg nem támogatja a [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) hanganyagot tartalmazó fájlokat (mivel ez a Dolby által örökölt kodeknek számít).

## <a name="manifests"></a>Jegyzékek

Media Services *dinamikus csomagolásban* a HLS, MPEG-DASH és Smooth streaming adatfolyam-ügyfél-jegyzékfájlok dinamikusan jönnek létre az URL-cím kiválasztó alapján.  

A jegyzékfájlok olyan adatfolyam-metaadatokat tartalmaznak, mint például a követés típusa (hang, videó vagy szöveg), a nyomon követési idő, a kezdő és a befejező időpont, a bitráta (Tulajdonságok), a nyelvek nyomon követése, a megjelenítési időszak (rögzített időtartamú ablak) és a video codec (FourCC). Arra is utasítja a lejátszót, hogy a következő töredéket olvassa be a rendelkezésre álló, valamint a helyükre tartozó következő lejátszható videó-töredékek adatainak megadásával. A töredékek (vagy szegmensek) a videotartalom tényleges "darabjai".

### <a name="examples"></a>Példák

#### <a name="hls"></a>HLS

Íme egy példa egy HLS manifest-fájlra, más néven HLS fő lejátszási listára: 

```
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-MEDIA:TYPE=AUDIO,GROUP-ID="audio",NAME="aac_eng_2_128041_2_1",LANGUAGE="eng",DEFAULT=YES,AUTOSELECT=YES,URI="QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)"
#EXT-X-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d,mp4a.40.2",AUDIO="audio"
QualityLevels(381048)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d",URI="QualityLevels(381048)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015,mp4a.40.2",AUDIO="audio"
QualityLevels(721495)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015",URI="QualityLevels(721495)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e,mp4a.40.2",AUDIO="audio"
QualityLevels(1154816)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e",URI="QualityLevels(1154816)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f,mp4a.40.2",AUDIO="audio"
QualityLevels(2217354)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f",URI="QualityLevels(2217354)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020,mp4a.40.2",AUDIO="audio"
QualityLevels(3579827)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020",URI="QualityLevels(3579827)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=139017,CODECS="mp4a.40.2",AUDIO="audio"
QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)
```

#### <a name="mpeg-dash"></a>MPEG-KÖTŐJEL

Íme egy példa egy MPEG-DASH manifest-fájlra, más néven MPEG-DASH Media Presentation Description (MPD):

```xml
<?xml version="1.0" encoding="UTF-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="static" mediaPresentationDuration="PT1M10.315S" minBufferTime="PT7S">
   <Period>
      <AdaptationSet id="1" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2" lang="en">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60160000" r="10" />
               <S d="41386666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="5_A_aac_eng_2_128041_2_1_1" bandwidth="128041" audioSamplingRate="48000" />
      </AdaptationSet>
      <AdaptationSet id="2" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60060000" r="10" />
               <S d="42375666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="1_V_video_1" bandwidth="3579827" width="1280" height="720" />
         <Representation id="1_V_video_2" bandwidth="2217354" codecs="avc1.64001F" width="960" height="540" />
         <Representation id="1_V_video_3" bandwidth="1154816" codecs="avc1.64001E" width="640" height="360" />
         <Representation id="1_V_video_4" bandwidth="721495" codecs="avc1.640015" width="480" height="270" />
         <Representation id="1_V_video_5" bandwidth="381048" codecs="avc1.64000D" width="320" height="180" />
      </AdaptationSet>
   </Period>
</MPD>
```
#### <a name="smooth-streaming"></a>Smooth Streaming

Példa Smooth Streaming manifest-fájlra:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="703146666" TimeScale="10000000">
   <StreamIndex Chunks="12" Type="audio" Url="QualityLevels({bitrate})/Fragments(aac_eng_2_128041_2_1={start time})" QualityLevels="1" Language="eng" Name="aac_eng_2_128041_2_1">
      <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="128041" FourCC="AACL" CodecPrivateData="1190" Channels="2" PacketSize="4" SamplingRate="48000" />
      <c t="0" d="60160000" r="11" />
      <c d="41386666" />
   </StreamIndex>
   <StreamIndex Chunks="12" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="5">
      <QualityLevel Index="0" Bitrate="3579827" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="0000000167640020ACD9405005BB011000003E90000EA600F18319600000000168EBECB22C" />
      <QualityLevel Index="1" Bitrate="2217354" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FACD940F0117EF01100000303E90000EA600F1831960000000168EBECB22C" />
      <QualityLevel Index="2" Bitrate="1154816" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EACD940A02FF9701100000303E90000EA600F162D960000000168EBECB22C" />
      <QualityLevel Index="3" Bitrate="721495" FourCC="H264" MaxWidth="480" MaxHeight="270" CodecPrivateData="0000000167640015ACD941E08FEB011000003E90000EA600F162D9600000000168EBECB22C" />
      <QualityLevel Index="4" Bitrate="381048" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DACD941419F9F011000003E90000EA600F14299600000000168EBECB22C" />
      <c t="0" d="60060000" r="11" />
      <c d="42375666" />
   </StreamIndex>
</SmoothStreamingMedia>
```

### <a name="naming-of-tracks-in-the-manifest"></a>A jegyzékben szereplő számok elnevezése

Ha meg van adva egy hangsáv neve az. ISM fájlban, Media Services egy elemet ad hozzá az a `Label` -n belül, `AdaptationSet` hogy megadja az adott hangsávokhoz tartozó anyagminta-információkat. Példa a kimeneti kötőjel jegyzékfájlra:

```xml
<AdaptationSet codecs="mp4a.40.2" contentType="audio" lang="en" mimeType="audio/mp4" subsegmentAlignment="true" subsegmentStartsWithSAP="1">
  <Label>audio_track_name</Label>
  <Role schemeIdUri="urn:mpeg:dash:role:2011" value="main"/>
  <Representation audioSamplingRate="48000" bandwidth="131152" id="German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg">
    <BaseURL>German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg.mp4</BaseURL>
  </Representation>
</AdaptationSet>
```

A lejátszó használhatja az `Label` elemet a felhasználói felületen való megjelenítéshez.

### <a name="signaling-audio-description-tracks"></a>A hangjelzések leírásának nyomon követése

Kísérőszöveget adhat a videóhoz, hogy az ügyfelek vizuálisan korlátozott számú ügyfelet a Kísérőszöveg figyelésével kövessék. A jegyzékfájlban hanganyagként kell jegyzeteket adni. Ehhez adja hozzá a "kisegítő adatok" és a "szerepkör" paramétereket az. ISM fájlhoz. Az Ön felelőssége, hogy ezeket a paramétereket helyesen adja meg, hogy hangvételt jelezzen a hanganyagok leírásaként. Például vegyen fel `<param name="accessibility" value="description" />` `<param name="role" value="alternate"` egy adott hangsávokat a és az. ISM fájlba. 

További információ: [útmutató a Description hangfrekvenciás követéséhez](signal-descriptive-audio-howto.md) .

#### <a name="smooth-streaming-manifest"></a>Smooth Streaming jegyzékfájl

Ha Smooth Streaming adatfolyamot játszik, a jegyzékfájl a `Accessibility` hangsávok értékeit és `Role` attribútumait fogja végrehajtani. Például az `Role="alternate" Accessibility="description"` elemben adja hozzá az `StreamIndex` elemet, hogy jelezze a hang leírását.

#### <a name="dash-manifest"></a>KÖTŐJEL jegyzékfájlja

A DASH manifest esetében a következő két elemet kell felvenni a hang leírásának jelzéséhez:

```xml
<Accessibility schemeIdUri="urn:mpeg:dash:role:2011" value="description"/>
<Role schemeIdUri="urn:mpeg:dash:role:2011" value="alternate"/>
```

#### <a name="hls-playlist"></a>HLS lejátszási lista

A HLS v7 és újabb verziók esetében a `(format=m3u8-cmaf)` lejátszási lista `AUTOSELECT=YES,CHARACTERISTICS="public.accessibility.describes-video"` a hang leírásának nyomon követése után is elvégezhető.

#### <a name="example"></a>Példa

További információ: [a hangleírás nyomon követése](signal-descriptive-audio-howto.md).

## <a name="dynamic-manifest"></a>Dinamikus jegyzékfájl

A játékosok számára eljuttatott zeneszámok, formátumok, bitráták és a bemutató időpontok számának szabályozásához dinamikus szűrést használhat a Media Services dinamikus csomagoló használatával. További információkért lásd: [a dinamikus csomagolóval kapcsolatos előzetes szűrési jegyzékek](filters-dynamic-manifest-concept.md).

## <a name="dynamic-encryption"></a>Dinamikus titkosítás

A *dinamikus titkosítás* használatával az élő vagy igény szerinti tartalmakat az AES-128 vagy a három nagy digitális jogkezelési (DRM) rendszerű rendszeren keresztül dinamikusan titkosíthatja: Microsoft PlayReady, Google Widevine és Apple Fairplay. A Media Services egy szolgáltatást is biztosít az AES-kulcsok és a DRM-licencek engedélyezésére a hitelesítő ügyfelek számára. További információ: [dinamikus titkosítás](drm-content-protection-concept.md).

> [!NOTE]
> A Widevine a Google Inc által biztosított szolgáltatás, és a Google, Inc. szolgáltatási és adatvédelmi szabályzatának feltételei vonatkoznak rá.

## <a name="more-information"></a>További információ

Tekintse meg [Azure Media Services közösségét](media-services-community.md) , hogy különböző módokon lásson kérdéseket, visszajelzést küldjön, és frissítéseket kérjen a Media Servicesról.

## <a name="need-help"></a>Segítségre van szüksége?

A támogatási jegyet az [új támogatási kérelemre](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)való navigálással nyithatja meg.

## <a name="next-steps"></a>Következő lépések

[Videók feltöltése, kódolása és streamelése](stream-files-tutorial-with-api.md)
