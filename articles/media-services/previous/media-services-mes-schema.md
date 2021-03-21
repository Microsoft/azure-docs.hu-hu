---
title: Media Encoder Standard séma | Microsoft Docs
description: Ez a cikk az XML-séma azon elemeit és típusait ismerteti, amelyeken a Media Encoder Standard-készletek alapulnak.
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
ms.openlocfilehash: f82e0c3f76dba05c3404b11e07c7130119ce0b9d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103015658"
---
# <a name="media-encoder-standard-schema"></a>Media Encoder Standard-séma

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Ez a cikk az XML-séma azon elemeit és típusait ismerteti, amelyeken a [Media Encoder standard-készletek](media-services-mes-presets-overview.md) alapulnak. A cikk magyarázatot ad az elemekről és azok érvényes értékeiről.  

## <a name="preset-root-element"></a><a name="Preset"></a> Beállításkészlet (gyökérelem)
Definiál egy kódolási beállításkészletet.  

### <a name="elements"></a>Elemek

| Név | Típus | Description |
| --- | --- | --- |
| **Kódolás** |[Kódolás](media-services-mes-schema.md#Encoding) |A gyökérelem azt jelzi, hogy a bemeneti forrásokat kódolni kell. |
| **Kimenetek** |[Kimenetek](media-services-mes-schema.md#Output) |A kívánt kimeneti fájlok gyűjteménye. |
| **StretchMode**<br/>minOccurs = "0"<br/>alapértelmezett = "AutoSize|xs: karakterlánc|A kimeneti képkeret méretének, kitöltésének, képpontjának vagy megjelenítési méretarányának szabályozása. A **StretchMode** az alábbi értékek egyike lehet: **none**, **AutoSize** (alapértelmezett) vagy **Automatikus méretezés**.<br/><br/>**Nincs**: szigorúan kövesse a kimeneti felbontást (például az előre beállított **szélességet** és **magasságot** ) anélkül, hogy figyelembe kellene venni a bemeneti videó képpont méretarányát vagy megjelenítési méretarányát. Ajánlott olyan forgatókönyvekben, mint például a [növénytermesztés](media-services-crop-video.md), ahol a kimeneti videó más oldalarányú, mint a bemenet. <br/><br/>Automatikus **méret**: a kimenet felbontása az előre megadott ablakban (szélesség * magasság) belül elfér. A kódoló azonban a szögletes (1:1) képpont oldalarányú kimeneti videót hoz létre. Ezért a kimeneti szélesség vagy a kimeneti magasság felülbírálható annak érdekében, hogy megfeleljen a bemenet megjelenítési méretarányának, kitöltés nélkül. Ha például a bemenet 1920 × 1080, és a kódolási beállításkészlet 1280x1280 kér, akkor a rendszer felülbírálja az előre beállított magasságot, a kimenet pedig a 1280x720-on, amely a 16:9-as bemeneti méretarányt tartja fenn. <br/><br/>**Automatikus méretezés**: ha szükséges, a kimeneti videót (vagy a levélszekrény vagy a pillarbox) adja meg a kívánt kimeneti felbontás tiszteletben tartásához, miközben a kimenetben lévő aktív videó régiójának a bemenettel megegyező oldalaránya van. Tegyük fel például, hogy a bemenet 1920 × 1080, és a kódolási beállításkészlet kéri a 1280x1280. Ezt követően a kimeneti videó a következő helyen lesz: 1280x1280, de tartalmaz egy "aktív videó" belső 1280x720 téglalapot, amelynek oldalaránya 16:9, és a levélszekrény-régiók 280 képpont magas a tetején és az alján. Egy másik példa, ha a bemenet 1440x1080, és a kódolási beállításkészlet kéri a 1280x720, akkor a kimenet a következő helyen található: 1280x720, amely a 960x720 belső téglalapját tartalmazza a 4:3 160-as oldalaránynál 

### <a name="attributes"></a>Attribútumok

| Név | Típus | Description |
| --- | --- | --- |
| **Verzió**<br/><br/> Kötelező |**xs: decimális** |Az előre definiált verzió. A következő korlátozások érvényesek: XS: fractionDigits Value = "1" és XS: minInclusive Value = "1", például **version = "1.0"**. |

## <a name="encoding"></a><a name="Encoding"></a> Kódolási
A következő elemek sorát tartalmazza:  

### <a name="elements"></a>Elemek

| Név | Típus | Description |
| --- | --- | --- |
| **H264Video** |[H264Video](media-services-mes-schema.md#H264Video) |A videó H. 264 kódolásának beállításai. |
| **AACAudio** |[AACAudio](media-services-mes-schema.md#AACAudio) |A hang AAC-kódolásának beállításai. |
| **BmpImage** |[BmpImage](media-services-mes-schema.md#BmpImage) |A BMP-rendszerkép beállításai. |
| **PngImage** |[PngImage](media-services-mes-schema.md#PngImage) |A PNG-rendszerkép beállításai. |
| **JpgImage** |[JpgImage](media-services-mes-schema.md#JpgImage) |A jpg-rendszerkép beállításai. |

## <a name="h264video"></a><a name="H264Video"></a> H264Video
### <a name="elements"></a>Elemek

| Név | Típus | Description |
| --- | --- | --- |
| **TwoPass**<br/><br/> minOccurs = "0" |**xs: logikai** |Jelenleg csak egymenetes kódolás támogatott. |
| **KeyFrameInterval**<br/><br/> minOccurs = "0"<br/><br/> **default = "00:00:02"** |**xs: idő** |Meghatározza a rögzített térközt a IDR-keretek között másodpercben. Más néven a GOP időtartama. Tekintse meg a **SceneChangeDetection** , amely azt szabályozza, hogy a kódoló el tudja-e térni ettől az értéktől. |
| **SceneChangeDetection**<br/><br/> minOccurs = "0"<br/><br/> default = "false" |**xs: logikai** |Ha igaz értékre van állítva, a kódoló megkísérli felderíteni a videó jelenetének változását, és beszúr egy IDR-keretet. |
| **Összetettsége**<br/><br/> minOccurs = "0"<br/><br/> default = "Balanced" |**xs: karakterlánc** |A kódolás sebessége és a videó minősége közötti kompromisszumot vezérli. A következő értékek egyike lehet: **sebesség**, **kiegyensúlyozott** vagy **minőségi**<br/><br/> Alapértelmezett: **kiegyensúlyozott** |
| **SyncMode**<br/><br/> minOccurs = "0" | |A szolgáltatás egy későbbi kiadásban lesz elérhető. |
| **H264Layers**<br/><br/> minOccurs = "0" |[H264Layers](media-services-mes-schema.md#H264Layers) |Kimeneti videó rétegek gyűjteménye |

### <a name="attributes"></a>Attribútumok

| Név | Típus | Description |
| --- | --- | --- |
| **Feltétel** |**xs: karakterlánc** | Ha a bemenet nem tartalmaz videót, érdemes lehet kényszeríteni a kódolót, hogy beillesszen egy monokróm videó követését. Ehhez használja a Condition = "InsertBlackIfNoVideoBottomLayerOnly" (a videó csak a legkisebb bitrátával való beszúrásához) vagy a Condition = "InsertBlackIfNoVideo" (videó beszúrásához az összes kimeneti bitrátánál) lehetőséget. További információkért tekintse meg [ezt](media-services-advanced-encoding-with-mes.md#no_video) a cikket.|

## <a name="h264layers"></a><a name="H264Layers"></a> H264Layers

Alapértelmezés szerint, ha olyan bemenetet küld a kódolónak, amely csak hangot tartalmaz, és nincs videó, a kimeneti eszköz csak hangadatokkal rendelkező fájlokat tartalmaz. Előfordulhat, hogy egyes játékosok nem tudják kezelni az ilyen kimeneti adatfolyamokat. A H264Video's **InsertBlackIfNoVideo** attribútum beállításával kényszerítheti a kódolót, hogy adjon hozzá egy video tracket az adott forgatókönyv kimenetéhez. További információkért tekintse meg [ezt](media-services-advanced-encoding-with-mes.md#no_video) a cikket.
              
### <a name="elements"></a>Elemek

| Név | Típus | Description |
| --- | --- | --- |
| **H264Layer**<br/><br/> minOccurs = "0" maxOccurs = "nem kötött" |[H264Layer](media-services-mes-schema.md#H264Layer) |H264-rétegek gyűjteménye. |

## <a name="h264layer"></a><a name="H264Layer"></a> H264Layer
> [!NOTE]
> A videó korlátai a [H264-szintek](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC#Levels) táblázatban leírt értékek alapján érhetők el.  
> 
> 

### <a name="elements"></a>Elemek

| Név | Típus | Description |
| --- | --- | --- |
| **Profil**<br/><br/> minOccurs = "0"<br/><br/> default = "automatikus" |**xs: karakterlánc** |A következő **XS: string** értékek egyike lehet: **automatikus**, **Alapterv**, **fő**, **magas**. |
| **Szintű**<br/><br/> minOccurs = "0"<br/><br/> default = "automatikus" |**xs: karakterlánc** | |
| **Sávszélességű**<br/><br/> minOccurs = "0" |**xs: int** |A videó réteghez használt, kbps-ben megadott bitrátát adja meg. |
| **MaxBitrate**<br/><br/> minOccurs = "0" |**xs: int** |A videó réteghez használt maximális sebesség kbps-ben megadva. |
| **BufferWindow**<br/><br/> minOccurs = "0"<br/><br/> default = "00:00:05" |**xs: idő** |A videó pufferének hossza |
| **Szélesség**<br/><br/> minOccurs = "0" |**xs: int** |A kimeneti videó keretének szélessége képpontban megadva<br/><br/> Jelenleg a szélességet és a magasságot kell megadnia. A szélességnek és a magasságnak páros számnak kell lennie. |
| **Magasság**<br/><br/> minOccurs = "0" |**xs: int** |A kimeneti videó keretének magassága képpontban megadva<br/><br/> Jelenleg a szélességet és a magasságot kell megadnia. A szélességnek és a magasságnak páros számnak kell lennie.|
| **BFrames**<br/><br/> minOccurs = "0" |**xs: int** |A (B) keretek száma a hivatkozási keretek között. |
| **ReferenceFrames**<br/><br/> minOccurs = "0"<br/><br/> alapértelmezett = "3" |**xs: int** |A GOP-ban található hivatkozási keretek száma. |
| **EntropyMode**<br/><br/> minOccurs = "0"<br/><br/> default = "CABAC" |**xs: karakterlánc** |A következő értékek egyike lehet: **CABAC** és **Cavlc**. |
| **Frameráta**<br/><br/> minOccurs = "0" |racionális szám |Meghatározza a kimeneti videó képkockasebességi sebességét. Használja az alapértelmezett "0/1" értéket, amely lehetővé teszi, hogy a kódoló ugyanazt a képarányt használja, mint a bemeneti videó. Az engedélyezett értékek a képkockák általános díjszabásának várhatók. Azonban minden érvényes racionális érték engedélyezett. Például 1/1 lenne 1 fps, és érvényes.<br/><br/> -12/1 (12 fps)<br/><br/> -15/1 (15 FPS)<br/><br/> -24/1 (24 fps)<br/><br/> -24000/1001 (23,976 fps)<br/><br/> -25/1 (25 fps)<br/><br/>  -30/1 (30 fps)<br/><br/> -30000/1001 (29,97 fps) <br/> <br/>**Megjegyzés** Ha egyéni beállításkészletet hoz létre a többszörös átviteli sebességű kódoláshoz, akkor az előre definiált összes rétegnek ugyanazt a frameráta értéket **kell** használnia.|
| **AdaptiveBFrame**<br/><br/> minOccurs = "0" |**xs: logikai** |Másolás az Azure Media Encoderből |
| **Szeletek**<br/><br/> minOccurs = "0"<br/><br/> alapértelmezett = "0" |**xs: int** |Meghatározza, hogy egy keret hány szeletre van osztva. Ajánlott az alapértelmezett használata. |

## <a name="aacaudio"></a><a name="AACAudio"></a> AACAudio
 A következő elemek és csoportok sorozatot tartalmazza.  

 Az AAC-vel kapcsolatos további információkért lásd: [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding).  

### <a name="elements"></a>Elemek

| Név | Típus | Description |
| --- | --- | --- |
| **Profil**<br/><br/> minOccurs = "0"<br/><br/> default = "AACLC" |**xs: karakterlánc** |A következő értékek egyike lehet: **AACLC**, **HEAACV1** vagy **HEAACV2**. |

### <a name="attributes"></a>Attribútumok

| Név | Típus | Description |
| --- | --- | --- |
| **Feltétel** |**xs: karakterlánc** |Ha úgy szeretné kényszeríteni a kódolót, hogy olyan objektumot hozzon létre, amely csendes hangsávot tartalmaz, ha a bemenet nem rendelkezik hanggal, adja meg a "InsertSilenceIfNoAudio" értéket.<br/><br/> Alapértelmezés szerint ha olyan bemenetet küld a kódolónak, amely csak videót tartalmaz, és nincs hang, akkor a kimeneti eszköz csak videó adatokat tartalmazó fájlokat tartalmaz. Előfordulhat, hogy egyes játékosok nem tudják kezelni az ilyen kimeneti adatfolyamokat. Ezzel a beállítással kényszerítheti a kódolót, hogy egy csendes hangsávot adjon hozzá a forgatókönyv kimenetéhez. |

### <a name="groups"></a>Csoportok

| Referencia | Description |
| --- | --- |
| [AudioGroup](media-services-mes-schema.md#AudioGroup)<br/><br/> minOccurs = "0" |Tekintse meg a [AudioGroup](media-services-mes-schema.md#AudioGroup) leírását, amelyből megtudhatja, hogy a megfelelő számú csatornát, mintavételi sebességet és átviteli sebességet szeretné-e beállítani az egyes profilokhoz. |

## <a name="audiogroup"></a><a name="AudioGroup"></a> AudioGroup
Az egyes profilokban érvényes értékekkel kapcsolatos részletekért tekintse meg az alábbi "hangkodek részletei" című táblázatot.  

### <a name="elements"></a>Elemek

| Név | Típus | Description |
| --- | --- | --- |
| **Csatornák**<br/><br/> minOccurs = "0" |**xs: int** |A kódolt Hangcsatornák száma. A következő érvényes beállítások érhetők el: 1, 2, 5, 6, 8.<br/><br/> Alapértelmezett: 2. |
| **SamplingRate**<br/><br/> minOccurs = "0" |**xs: int** |A hangmintavételezési sebesség (Hz) megadva. |
| **Sávszélességű**<br/><br/> minOccurs = "0" |**xs: int** |A hang kódolásához használt bitráta, kbps-ban megadva. |

### <a name="audio-codec-details"></a>Hangkodek részletei

Hangkodek|Részletek  
-----------------|---  
**AACLC** |1:<br/><br/> -11025:8 &lt; = bitráta &lt; 16<br/><br/> -12000:8 &lt; = bitráta &lt; 16<br/><br/> -16000:8 &lt; = bitráta &lt; 32<br/><br/>-22050:24 &lt; = bitráta &lt; 32<br/><br/> -24000:24 &lt; = bitráta &lt; 32<br/><br/> -32000:32 &lt; = bitráta &lt; = 192<br/><br/> -44100:56 &lt; = bitráta &lt; = 288<br/><br/> -48000:56 &lt; = bitráta &lt; = 288<br/><br/> -88200:128 &lt; = bitráta &lt; = 288<br/><br/> -96000:128 &lt; = bitráta &lt; = 288<br/><br/> 2:<br/><br/> -11025:16 &lt; = bitráta &lt; 24<br/><br/> -12000:16 &lt; = bitráta &lt; 24<br/><br/> -16000:16 &lt; = bitráta &lt; 40<br/><br/> -22050:32 &lt; = bitráta &lt; 40<br/><br/> -24000:32 &lt; = bitráta &lt; 40<br/><br/> -32000:40 &lt; = bitráta &lt; = 384<br/><br/> -44100:96 &lt; = bitráta &lt; = 576<br/><br/> -48000:96 &lt; = bitráta &lt; = 576<br/><br/> -88200:256 &lt; = bitráta &lt; = 576<br/><br/> -96000:256 &lt; = bitráta &lt; = 576<br/><br/> 5/6:<br/><br/> -32000:160 &lt; = bitráta &lt; = 896<br/><br/> -44100:240 &lt; = bitráta &lt; = 1024<br/><br/> -48000:240 &lt; = bitráta &lt; = 1024<br/><br/> -88200:640 &lt; = bitráta &lt; = 1024<br/><br/> -96000:640 &lt; = bitráta &lt; = 1024<br/><br/> 8:<br/><br/> -32000:224 &lt; = bitráta &lt; = 1024<br/><br/> -44100:384 &lt; = bitráta &lt; = 1024<br/><br/> -48000:384 &lt; = bitráta &lt; = 1024<br/><br/> -88200:896 &lt; = bitráta &lt; = 1024<br/><br/> -96000:896 &lt; = bitráta &lt; = 1024  
**HEAACV1** |1:<br/><br/> -22050: bitráta = 8<br/><br/> -24000:8 &lt; = bitráta &lt; = 10<br/><br/> -32000:12 &lt; = bitráta &lt; = 64<br/><br/> -44100:20 &lt; = bitráta &lt; = 64<br/><br/> -48000:20 &lt; = bitráta &lt; = 64<br/><br/> -88200: bitráta = 64<br/><br/> 2:<br/><br/> -32000:16 &lt; = bitráta &lt; = 128<br/><br/> -44100:16 &lt; = bitráta &lt; = 128<br/><br/> -48000:16 &lt; = bitráta &lt; = 128<br/><br/> -88200:96 &lt; = bitráta &lt; = 128<br/><br/> -96000:96 &lt; = bitráta &lt; = 128<br/><br/> 5/6:<br/><br/> -32000:64 &lt; = bitráta &lt; = 320<br/><br/> -44100:64 &lt; = bitráta &lt; = 320<br/><br/> -48000:64 &lt; = bitráta &lt; = 320<br/><br/> -88200:256 &lt; = bitráta &lt; = 320<br/><br/> -96000:256 &lt; = bitráta &lt; = 320<br/><br/> 8:<br/><br/> -32000:96 &lt; = bitráta &lt; = 448<br/><br/> -44100:96 &lt; = bitráta &lt; = 448<br/><br/> -48000:96 &lt; = bitráta &lt; = 448<br/><br/> -88200:384 &lt; = bitráta &lt; = 448<br/><br/> -96000:384 &lt; = bitráta &lt; = 448  
**HEAACV2** |2:<br/><br/> -22050:8 &lt; = bitráta &lt; = 10<br/><br/> -24000:8 &lt; = bitráta &lt; = 10<br/><br/> -32000:12 &lt; = bitráta &lt; = 64<br/><br/> -44100:20 &lt; = bitráta &lt; = 64<br/><br/> -48000:20 &lt; = bitráta &lt; = 64<br/><br/> -88200:64 &lt; = bitráta &lt; = 64  
  
## <a name="clip"></a><a name="Clip"></a> Klip
### <a name="attributes"></a>Attribútumok

| Név | Típus | Description |
| --- | --- | --- |
| **StartTime** |**xs: időtartam** |Megadja a bemutató kezdő időpontját. A kezdő időpont értékének meg kell egyeznie a bemeneti videó abszolút időbélyegzővel. Ha például a bemeneti videó első képkockája 12:00:10.000, akkor a kezdő időpontnak legalább 12:00:10.000 vagy nagyobbnak kell lennie. |
| **Időtartam** |**xs: időtartam** |Meghatározza a bemutató időtartamát (például egy átfedés megjelenését a videóban). |

## <a name="output"></a><a name="Output"></a> Kimeneti
### <a name="attributes"></a>Attribútumok

| Név | Típus | Description |
| --- | --- | --- |
| **FileName** |**xs: karakterlánc** |A kimeneti fájl neve.<br/><br/> A következő táblázatban ismertetett makrókat használhatja a kimeneti fájlnevek létrehozásához. Például:<br/><br/> **"Outputs": [{"FileName": "{basename}*{felbontás}*{bitráta}. mp4", "Format": {"type": "MP4Format"}}]** |

### <a name="macros"></a>Makrók

| Makró | Description |
| --- | --- |
| **{Basename}** |Ha a VoD-kódolást végzi, a {basename} a bemeneti eszköz elsődleges fájljának AssetFile.Name tulajdonságának első 32 karaktere.<br/><br/> Ha a bemeneti eszköz egy élő Archívum, akkor a {basename} a kiszolgálói jegyzékfájl trackName attribútumaiból származik. Ha a TopBitrate használatával küld el egy alklip feladatot, a következő módon: "<VideoStream \> TopBitrate</VideoStream \> ", és a kimeneti fájl tartalmaz videót, akkor a {basename} a trackName első 32 karaktere a legmagasabb sávszélességgel.<br/><br/> Ha ehelyett beküld egy alklip feladatot az összes bemeneti bitráta használatával (például "<VideoStream \> * </VideoStream \> "), és a kimeneti fájl tartalmazza a videót, akkor a {basename} a megfelelő videó réteg trackName első 32 karaktere. |
| **Codec** |Leképezi a "H264"-t a videóhoz és az "AAC" hanghoz. |
| **Sávszélességű** |A célként megadott videó bitrátája, ha a kimeneti fájl videó-és hangátviteli sebességet tartalmaz, vagy ha a kimeneti fájl csak hanganyagot tartalmaz. A használt érték a sávszélesség Kbit/s-ban megadva. |
| **Csatorna** |A Hangcsatornák száma, ha a fájl hangot tartalmaz. |
| **Szélessége** |A videó szélessége képpontban, a kimeneti fájlban, ha a fájl tartalmaz videót. |
| **Magasság** |A videó magassága képpontban, a kimeneti fájlban, ha a fájl tartalmaz videót. |
| **Kiterjesztés** |Örökli a kimeneti fájl "type" tulajdonságát. A kimeneti fájl nevének kiterjesztése a következő lehet: "MP4", "TS", "jpg", "png" vagy "BMP". |
| **Index** |A miniatűr megadása kötelező. Csak egyszer kell jelen lennie. |

## <a name="video-complex-type-inherits-from-codec"></a><a name="Video"></a> Videó (az összetett típus örökli a kodeket)
### <a name="attributes"></a>Attribútumok

| Név | Típus | Description |
| --- | --- | --- |
| **Kezdés** |**xs: karakterlánc** | |
| **Lépés** |**xs: karakterlánc** | |
| **Tartomány** |**xs: karakterlánc** | |
| **PreserveResolutionAfterRotation** |**xs: logikai** |Részletes magyarázatot a következő szakaszban talál: [PreserveResolutionAfterRotation](media-services-mes-schema.md#PreserveResolutionAfterRotation) |

### <a name="preserveresolutionafterrotation"></a><a name="PreserveResolutionAfterRotation"></a> PreserveResolutionAfterRotation
Azt javasoljuk, hogy a **PreserveResolutionAfterRotation** jelzőt a (z) százalékban kifejezett feloldási értékekkel együtt használja (width = "100%", height = "100%").  

Alapértelmezés szerint a kódolási feloldási beállítások (szélesség, magasság) a Media Encoder Standard (MES) előkészletekben a 0 fokos rotációs videókat célozzák meg. Ha például a bemeneti videó nulla fokos rotációs 1280x720, akkor az alapértelmezett alapbeállítások biztosítják, hogy a kimenet ugyanazzal a megoldással legyen.  

![MESRoation1](./media/media-services-shemas/media-services-mes-roation1.png) 

Ha a bemeneti videó nem nulla elforgatással lett rögzítve (például egy Smartphone vagy egy függőlegesen megőrzött), akkor a MES alapértelmezés szerint a kódolás feloldási beállításait (szélesség, magasság) alkalmazza a bemeneti videóra, majd kompenzálja az elforgatást. Például tekintse meg az alábbi ábrát. Az előre definiált szélesség = "100%", height = "100%", amelyet a MES úgy értelmez, hogy a kimenetnek 1280 képpont szélesnek és 720 képpont magasnak kell lennie. A videó elforgatása után a kép lekicsinyíti a képet, hogy illeszkedjen az adott ablakhoz, amely a bal és a jobb oldali oszlopos területekhez vezet.  

![MESRoation2](./media/media-services-shemas/media-services-mes-roation2.png) 

Azt is megteheti, hogy a **PreserveResolutionAfterRotation** jelzőt használja, és az "igaz" értéket adja meg (az alapértelmezett érték a "false"). Tehát ha az előre beállított szélesség = "100%", height = "100%", a PreserveResolutionAfterRotation értéke pedig "true", a bemeneti videó, amely a 1280 képpont széles és a 720 képpont magas, a 90-fokos rotációs kimenet pedig nulla fokos rotációt eredményez, de az 720 képpont széles és 1280 képpont magas. Tekintse meg a következő képet:  

![MESRoation3](./media/media-services-shemas/media-services-mes-roation3.png) 

## <a name="formatgroup-group"></a><a name="FormatGroup"></a> FormatGroup (csoport)
### <a name="elements"></a>Elemek

| Név | Típus | Description |
| --- | --- | --- |
| **BmpFormat** |**BmpFormat** | |
| **PngFormat** |**PngFormat** | |
| **JpgFormat** |**JpgFormat** | |

## <a name="bmplayer"></a><a name="BmpLayer"></a> BmpLayer
### <a name="element"></a>Elem

| Név | Típus | Description |
| --- | --- | --- |
| **Szélesség**<br/><br/> minOccurs = "0" |**xs: int** | |
| **Magasság**<br/><br/> minOccurs = "0" |**xs: int** | |

### <a name="attributes"></a>Attribútumok

| Név | Típus | Description |
| --- | --- | --- |
| **Feltétel** |**xs: karakterlánc** | |

## <a name="pnglayer"></a><a name="PngLayer"></a> PngLayer
### <a name="element"></a>Elem

| Név | Típus | Description |
| --- | --- | --- |
| **Szélesség**<br/><br/> minOccurs = "0" |**xs: int** | |
| **Magasság**<br/><br/> minOccurs = "0" |**xs: int** | |

### <a name="attributes"></a>Attribútumok

| Név | Típus | Description |
| --- | --- | --- |
| **Feltétel** |**xs: karakterlánc** | |

## <a name="jpglayer"></a><a name="JpgLayer"></a> JpgLayer
### <a name="element"></a>Elem

| Név | Típus | Description |
| --- | --- | --- |
| **Szélesség**<br/><br/> minOccurs = "0" |**xs: int** | |
| **Magasság**<br/><br/> minOccurs = "0" |**xs: int** | |
| **Minőség**<br/><br/> minOccurs = "0" |**xs: int** |Érvényes értékek: 1 (legrosszabb) – 100 (legjobb) |

### <a name="attributes"></a>Attribútumok

| Név | Típus | Description |
| --- | --- | --- |
| **Feltétel** |**xs: karakterlánc** | |

## <a name="pnglayers"></a><a name="PngLayers"></a> PngLayers
### <a name="elements"></a>Elemek

| Név | Típus | Description |
| --- | --- | --- |
| **PngLayer**<br/><br/> minOccurs = "0" maxOccurs = "nem kötött" |[PngLayer](media-services-mes-schema.md#PngLayer) | |

## <a name="bmplayers"></a><a name="BmpLayers"></a> BmpLayers
### <a name="elements"></a>Elemek

| Név | Típus | Description |
| --- | --- | --- |
| **BmpLayer**<br/><br/> minOccurs = "0" maxOccurs = "nem kötött" |[BmpLayer](media-services-mes-schema.md#BmpLayer) | |

## <a name="jpglayers"></a><a name="JpgLayers"></a> JpgLayers
### <a name="elements"></a>Elemek

| Név | Típus | Description |
| --- | --- | --- |
| **JpgLayer**<br/><br/> minOccurs = "0" maxOccurs = "nem kötött" |[JpgLayer](media-services-mes-schema.md#JpgLayer) | |

## <a name="bmpimage-complex-type-inherits-from-video"></a><a name="BmpImage"></a> BmpImage (az összetett típus örökli a videóból)
### <a name="elements"></a>Elemek

| Név | Típus | Description |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs = "0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Png-rétegek |

## <a name="jpgimage-complex-type-inherits-from-video"></a><a name="JpgImage"></a> JpgImage (az összetett típus örökli a videóból)
### <a name="elements"></a>Elemek

| Név | Típus | Description |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs = "0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Png-rétegek |

## <a name="pngimage-complex-type-inherits-from-video"></a><a name="PngImage"></a> PngImage (az összetett típus örökli a videóból)
### <a name="elements"></a>Elemek

| Név | Típus | Description |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs = "0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Png-rétegek |

## <a name="examples"></a>Példák
Tekintse meg a séma alapján létrehozott XML-előállítók példáit: a [MES (Media Encoder standard) feladatának előbeállításai](media-services-mes-presets-overview.md).

## <a name="next-steps"></a>Következő lépések
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

