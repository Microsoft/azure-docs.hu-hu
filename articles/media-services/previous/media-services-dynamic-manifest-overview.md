---
title: Szűrők és dinamikus jegyzékfájlok | Microsoft Docs
description: Ez a témakör azt ismerteti, hogyan hozhatók létre szűrők, hogy az ügyfél egy stream adott szakaszait továbbítsa. A Media Services dinamikus jegyzékfájlokat hoz létre a szelektív streaming archiválásához.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ff102765-8cee-4c08-a6da-b603db9e2054
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 55bf597d8df18085fc9a500a9213a6abe59fd5e3
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106108252"
---
# <a name="filters-and-dynamic-manifests"></a>Szűrők és dinamikus jegyzékek

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector" title1="Válassza ki a használt Media Services verzióját:"]
> * [2-es verzió](media-services-dynamic-manifest-overview.md)
> * [3\. verzió](../latest/filters-dynamic-manifest-concept.md)

A 2,17-es verziótól kezdődően a Media Services lehetővé teszi az eszközökhöz tartozó szűrők megadását. Ezek a szűrők olyan kiszolgálóoldali szabályok, amelyek lehetővé teszik az ügyfelek számára a következő műveleteket: csak a videó egy szakaszának lejátszása (a teljes videó lejátszása helyett), vagy csak az adott eszköz által kezelhető hang-és videó-átadások egy részhalmazát kell megadnia (az objektumhoz társított összes kiadatás helyett). Az eszközök szűrése az ügyfél által a megadott szűrő (k) alapján a videó streamre való továbbítására szolgáló **dinamikus jegyzékfájlon** keresztül érhető el.

Ez a témakör azokat a gyakori forgatókönyveket ismerteti, amelyekben a szűrők használata hasznos lehet az ügyfeleknek, és olyan témakörökre mutató hivatkozásokat tartalmaz, amelyek bemutatják, hogyan hozhat létre programozott módon a szűrőket.

## <a name="overview"></a>Áttekintés
A tartalomnak az ügyfeleknek való továbbításakor (élő események vagy igény szerinti videó közvetítése) a cél egy kiváló minőségű videó továbbítása különböző eszközökre különböző hálózati körülmények között. A cél eléréséhez tegye a következőket:

* kódolja a streamet többszörös sávszélességű ([adaptív sávszélességű](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)) video streambe (ez a minőség és a hálózati feltételekkel foglalkozik) 
* használjon Media Services [dinamikus csomagolást](media-services-dynamic-packaging-overview.md) , hogy dinamikusan újracsomagolja a streamet különböző protokollokra (ez a streaming különböző eszközökön is gondoskodik). Media Services támogatja a következő adaptív sávszélességű adatfolyam-technológiák szállítását: HTTP Live Streaming (HLS), Smooth Streaming és MPEG DASH. 

### <a name="manifest-files"></a>Jegyzékfájlok
Ha adategységet kódol az adaptív sávszélességű adatfolyam-továbbításhoz, létrejön egy **jegyzékfájl** (Playlist) fájl (a fájl Text-alapú vagy XML-alapú). A **jegyzékfájl** olyan adatfolyam-metaadatokat tartalmaz, mint például a követés típusa (hang, videó vagy szöveg), a nyomon követési idő, a kezdő és a záró időpont, a bitráta (Tulajdonságok), a nyelvek nyomon követése, a bemutató ablak (rögzített időtartam csúszó ablaka), video codec (FourCC). Arra is utasítja a lejátszót, hogy a következő töredéket kérje le a következő lejátszható videó töredékekről és azok helyéről. A töredékek (vagy szegmensek) a videotartalom tényleges "darabjai".

Példa egy jegyzékfájlra: 

```xml
<?xml version="1.0" encoding="UTF-8"?>    
<SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="330187755" TimeScale="10000000">

<StreamIndex Chunks="17" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="8">
<QualityLevel Index="0" Bitrate="5860941" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931300016E360000E4E1FF8C7076850A4580000000168E9093525" />
<QualityLevel Index="1" Bitrate="4602724" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931100011EDC00002CD29FF8C7076850A45800000000168E9093525" />
<QualityLevel Index="2" Bitrate="3319311" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="000000016764001FAC2CA5014016EC054808080A00000300020000030064C0800067C28000103667F8C7076850A4580000000168E9093525" />
<QualityLevel Index="3" Bitrate="2195119" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C1000044AA0000ABA9FE31C1DA14291600000000168E9093525" />
<QualityLevel Index="4" Bitrate="1469881" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C04000B71A0000E4E1FF8C7076850A4580000000168E9093525" />
<QualityLevel Index="5" Bitrate="978815" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C08001E8480004C4B7F8C7076850A45800000000168E9093525" />
<QualityLevel Index="6" Bitrate="638374" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C080013D60000C65DFE31C1DA1429160000000168E9093525" />
<QualityLevel Index="7" Bitrate="388851" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DAC2CA505067E7C054830303200000300020000030064C040030D40003D093F8C7076850A45800000000168E9093525" />

<c t="0" d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="9600000"/>
</StreamIndex>


<StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_128kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_128kbps">
<QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="125658" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />

<c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>


<StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_56kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_56kbps">
<QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="53655" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />

<c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>

</SmoothStreamingMedia>
```

### <a name="dynamic-manifests"></a>Dinamikus jegyzékek
Előfordulhat, hogy az ügyfélnek több rugalmasságra van [szüksége, mint](media-services-dynamic-manifest-overview.md#scenarios) amit az alapértelmezett adategység jegyzékfájljában ismertetünk. Például:

* Eszköz-specifikus: csak a megadott kiadatások és/vagy a megadott nyelvi sávok továbbítására szolgál, amelyek a tartalom lejátszásához használhatók ("kiadatási szűrés"). 
* Csökkentse a jegyzékfájlt az élő események ("alklipek szűrése") alklipének megjelenítéséhez.
* Egy videó elejének vágása ("videó vágása").
* A bemutató ablak (DVR) módosítása annak érdekében, hogy korlátozott hosszúságú legyen a DVR ablak a lejátszóban ("bemutató ablakának módosítása").

Ennek a rugalmasságnak a megvalósításához Media Services az előre definiált [szűrők](media-services-dynamic-manifest-overview.md#filters)alapján **dinamikus jegyzékfájlokat** kínál.  Miután definiálta a szűrőket, az ügyfelek felhasználhatják a videó egy adott kiadatásának vagy alvideoklipének továbbítására. A szűrő (ka) t a folyamatos átviteli URL-címben határozzák meg. A szűrők alkalmazhatók a [dinamikus csomagolás](media-services-dynamic-packaging-overview.md)által támogatott adaptív sávszélességű adatfolyam-továbbítási protokollokra: HLS, MPEG-DASH és Smooth streaming. Például:

MPEG DASH URL-cím szűrővel

`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=MyLocalFilter)`

URL-cím Smooth Streaming szűrővel

`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyLocalFilter)`


A tartalmak továbbításával és a streaming URL-címek létrehozásával kapcsolatos további információkért lásd a [tartalom áttekintését](media-services-deliver-content-overview.md).

> [!NOTE]
> Vegye figyelembe, hogy a dinamikus jegyzékfájlok nem változtatják meg az adott eszközre vonatkozó adategységet és az alapértelmezett jegyzékfájlt. Az ügyfél dönthet úgy, hogy szűrővel vagy anélkül is kér streamet. 
> 
> 

### <a name="filters"></a><a id="filters"></a>Szűrők
Az eszközök két típusa létezik: 

* Globális szűrők (a Azure Media Services fiókban lévő összes eszközre alkalmazható, a fiók élettartama) és 
* Helyi szűrők (csak olyan objektumra alkalmazhatók, amelynél a szűrő a létrehozáskor hozzá volt rendelve, az eszköz élettartama). 

A globális és a helyi szűrési típusok pontosan ugyanazok a tulajdonságok. A kettő között a fő különbség, hogy mely forgatókönyvek esetében alkalmasabb a Filer-típus. A globális szűrők általában az eszközök profiljaihoz (kiadatási szűréshez) alkalmasak, ahol helyi szűrők használhatók egy adott eszköz kivágására.

## <a name="common-scenarios"></a><a id="scenarios"></a>Gyakori forgatókönyvek
Ahogy korábban is említettük, a tartalomnak az ügyfeleknek való továbbításakor (élő események vagy igény szerinti videó közvetítése) a cél az, hogy magas színvonalú videót nyújtson be különböző hálózati körülmények között különböző eszközökre. Emellett előfordulhat, hogy egyéb követelmények is vannak, amelyek az eszközök szűrését és a **dinamikus jegyzékfájlok** használatát igénylik. A következő részekben rövid áttekintést talál a különböző szűrési forgatókönyvekről.

* Csak a hang-és videó-továbbítások egy részhalmazát adhatja meg, amelyeket bizonyos eszközök kezelhetnek (az objektumhoz társított összes kiadatás helyett). 
* Egy videó csak egy szakaszát játssza le (a teljes videó lejátszása helyett).
* A DVR-bemutató ablakának módosítása.

## <a name="rendition-filtering"></a>Kiadatás szűrése
Dönthet úgy is, hogy az eszközt több kódolási profilba kódolja (H. 264 alapkonfiguráció, H. 264 magas, AACL, AACH, Dolby Digital Plus) és több minőségi bitrátát. Azonban nem minden ügyféleszközök fogja támogatni az összes eszköz profilját és bitrátáját. Például a régebbi Android-eszközök csak a H. 264 Baseline + AACL támogatják. Nagyobb bitsebességek küldése olyan eszközre, amely nem tudja beolvasni az előnyöket, a hulladékok sávszélességét és az eszközök számítási felszámítását. Az eszköznek az összes megadott információt dekódolnia kell, csak a megjelenítéshez.

A dinamikus jegyzékfájl használatával olyan eszközöket hozhat létre, mint például a mobil, a konzol, a HD/SD stb., valamint az egyes profilok részét képező zeneszámokat és tulajdonságokat is.

![Példa a kiadatás szűrésére][renditions2]

A következő példában egy kódolót használtak egy köztes eszköz kódolásához hét ISO MP4 (180p és 1080p között). A kódolt eszköz dinamikusan becsomagolható a következő folyamatos átviteli protokollok bármelyikére: HLS, Smooth és MPEG DASH.  A diagram tetején megjelenik az objektum HLS-jegyzéke, amely nem tartalmaz szűrőket (ez mind a hét összes leképezését tartalmazza).  A bal alsó sarokban megjelenik a HLS jegyzékfájl, amelybe az "ott" nevű szűrő lett alkalmazva. Az "ott" szűrő azt adja meg, hogy a 1Mbps alatti összes bitrátát el szeretné távolítani, ami a válaszban az alsó két minőségi szintet eredményezi. A jobb alsó sarokban megjelenik a HLS jegyzékfájl, amelybe a "Mobile" nevű szűrő lett alkalmazva. A "mobil" szűrő meghatározza, hogy el lehessen távolítani azokat a leképezéseket, amelyekben a felbontás nagyobb, mint 720p, ami azt eredményezte, hogy a két 1080p-kiadatás kikerül.

![Kiadatás szűrése][renditions1]

## <a name="removing-language-tracks"></a>Nyelvi nyomon követések eltávolítása
Az eszközök több hangnyelvt is tartalmazhatnak, például angol, spanyol, francia stb. Általában a Player SDK-kezelők alapértelmezett hangsáv-kiválasztási és elérhető hangsávok felhasználónként választhatók ki. Ez kihívást jelent az ilyen játékos SDK-k fejlesztéséhez, és ez különböző implementációkat igényel az eszközre jellemző Player-keretrendszerek között. Emellett bizonyos platformokon a lejátszó API-k korlátozottak, és nem tartalmazzák a hangkijelölési funkciót, ahol a felhasználók nem választhatják ki vagy nem változtathatják meg az alapértelmezett hangsávot. Az Asset Filters használatával szabályozhatja a viselkedést olyan szűrők létrehozásával, amelyek csak a kívánt hangnyelveket tartalmazzák.

![Nyelvi nyomon követések szűrése][language_filter]

## <a name="trimming-start-of-an-asset"></a>Eszköz kivágásának kezdete
A legtöbb élő közvetítési eseménynél a kezelők a tényleges esemény előtt futtassanak teszteket. Például tartalmazhatnak egy, az esemény megkezdése előtt megjelenő előlappal: "a program hamarosan megkezdődik". Ha a program archiválást tartalmaz, a rendszer archiválja a teszt-és az adattárat, és tartalmazza a bemutatót is. Ezek az információk azonban nem jelennek meg az ügyfelek számára. A dinamikus jegyzékfájl használatával létrehozhat egy kezdési időpontot, és eltávolíthatja a nemkívánatos adatokból a jegyzékfájlt.

![Vágás kezdete][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>Alklipek (nézetek) létrehozása élő archívumból
Sok élő esemény hosszú ideig fut, és az élő archiválás több eseményt is tartalmazhat. Az élő esemény vége után a műsorszolgáltatók érdemes lehet az élő archívumot feltörni a logikai programok indítására és leállítására. Ezután tegye közzé ezeket a virtuális programokat külön, az élő Archívum feldolgozása után, és ne hozzon létre külön objektumokat (amely nem használja ki a meglévő gyorsítótárazott töredékek előnyeit a CDNs). Ilyen virtuális programok például a labdarúgás vagy kosárlabda játékának, a baseball inning-nek vagy bármely sportesemény egyes eseményeinek a negyedévei.

A dinamikus jegyzékfájl használatával szűrőket hozhat létre a kezdési és befejezési időpontok alapján, és virtuális nézeteket hozhat létre az élő Archívum tetején. 

![Alklip szűrője][subclip_filter]

Szűrt eszköz:

![Síelés][skiing]

## <a name="adjusting-presentation-window-dvr"></a>Bemutató ablak módosítása (DVR)
A Azure Media Services jelenleg körkörös archiválást biztosít, ahol az időtartam 5 perc és 25 óra között konfigurálható. A jegyzékfájlok szűrésével az Archívum felső részén, az adathordozó törlése nélkül hozhat létre egy gördülő DVR-ablakot. A műsorszolgáltatók számos szituációban szeretnék biztosítani, hogy az élő Edge-vel való áthelyezés és a nagyobb archiválási időszak megtartása mellett egy korlátozott DVR-ablakot is biztosítson. A műsorszolgáltató érdemes lehet a DVR ablakon kívüli adatmennyiséget használni a klipek kiemeléséhez, vagy különböző DVR-ablakokat szeretne biztosítani különböző eszközökhöz. Előfordulhat például, hogy a mobileszközök többsége nem kezeli a nagyméretű DVR-ablakokat (a mobileszközök esetében 2 perces DVR-ablakot és asztali ügyfelek számára egy órát is igénybe vehet).

![DVR ablak][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>LiveBackoff módosítása (élő pozíció)
A jegyzékfájl szűrésével több másodpercet lehet eltávolítani egy élő program élő szélétől. A szűrés lehetővé teszi a műsorszolgáltatók számára, hogy megtekintsék a bemutatót az előnézeti közzétételi ponton, és hirdetmények beszúrási pontjait hozzanak létre, mielőtt a megtekintők megkapják a streamet (30 másodpercen belül A műsorszolgáltatók ezután leküldhetik ezeket a hirdetményeket az ügyfél-keretrendszerekbe, hogy a hirdetmények megkezdése előtt megkapják és feldolgozzák az információkat.

A hirdetmények támogatása mellett a LiveBackoff beállítással is beállíthatja a megjelenítők pozícióját, így ha az ügyfelek elsodródnak, és az élő peremhálózati állapotba kerülnek, a HTTP 404 vagy 412 hiba miatt továbbra is kaphatnak töredékeket a kiszolgálóról.

![livebackoff_filter][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Több szabály kombinálása egyetlen szűrőben
Egyetlen szűrőben több szűrési szabályt is egyesítheti. Például megadhat egy "tartományhoz tartozó szabályt", amellyel eltávolíthatja az élő archívumból a bevezetéseket, és kiszűrheti a rendelkezésre álló bitrátákat is. Több szűrési szabály alkalmazása esetén a végeredmény az összes szabály metszete.

![több szabály][multiple-rules]

## <a name="create-filters-programmatically"></a>Szűrők létrehozása programozott módon
A következő cikk a szűrőkhöz kapcsolódó entitásokat ismerteti Media Services. A cikk azt is bemutatja, hogyan hozhatók létre programozott módon szűrők.  

[Hozzon létre szűrőket REST API](media-services-rest-dynamic-manifest.md)-kkal.

## <a name="combining-multiple-filters-filter-composition"></a>Több szűrő egyesítése (szűrő összetétele)
Egyetlen URL-címben több szűrőt is egyesíthet. 

Az alábbi forgatókönyv azt mutatja be, hogy miért érdemes a szűrőket egyesíteni:

1. A videók minőségének korlátozásához szűrnie kell a videó tulajdonságait (például Android vagy iPAD). A nemkívánatos tulajdonságok eltávolításához létre kell hoznia egy, az eszköz profiljaihoz megfelelő globális szűrőt. A cikkben korábban említettek szerint globális szűrők is használhatók az azonos Media Services-fiókkal rendelkező összes eszközhöz további társítás nélkül. 
2. Egy eszköz kezdési és befejezési időpontját is el szeretné metszeni. Ennek eléréséhez hozzon létre egy helyi szűrőt, és állítsa be a kezdő és a záró időpontot. 
3. Mindkét szűrőt kombinálni kívánja (kombináció nélkül, minőségi szűrést kell hozzáadnia a kivágási szűrőhöz, amely nehezebbé teszi a szűrési használatot).

A szűrők egyesítéséhez pontosvesszővel elválasztva kell beállítania a szűrő nevét a jegyzékfájl/lista URL-címére. Tegyük fel, hogy rendelkezik egy *MyMobileDevice* nevű szűrővel, amely a tulajdonságokat szűri, és egy másik nevű *MyStartTime* rendelkezik egy adott kezdési időpont beállításához. A következőhöz hasonló módon kombinálhatja őket:

`http://teststreaming.streaming.mediaservices.windows.net/3d56a4d-b71d-489b-854f-1d67c0596966/64ff1f89-b430-43f8-87dd-56c87b7bd9e2.ism/Manifest(filter=MyMobileDevice;MyStartTime)`

Legfeljebb három szűrőt lehet kombinálni. 

További információkért tekintse meg [ezt a](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blogot.

## <a name="know-issues-and-limitations"></a>Problémák és korlátozások
* A dinamikus jegyzékfájl a GOP határain (kulcstárolókban) működik, ezért a Kivágás a GOP pontossággal rendelkezik. 
* A helyi és a globális szűrőkhöz ugyanazt a nevet használhatja. A helyi szűrők magasabb prioritással rendelkeznek, és felülbírálják a globális szűrőket.
* Ha frissít egy szűrőt, akár 2 percet is igénybe vehet, amíg a streaming-végpont frissíti a szabályokat. Ha a tartalom bizonyos szűrők (és a proxyk és CDN-gyorsítótárak gyorsítótárazása) használatával lett kézbesítve, akkor a szűrők frissítése a hibákhoz vezethet. A szűrő frissítése után ajánlott törölni a gyorsítótárat. Ha ez a beállítás nem lehetséges, érdemes lehet egy másik szűrőt használni.

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még:
[Tartalom továbbítása az ügyfeleknek – áttekintés](media-services-deliver-content-overview.md)

[renditions1]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/media-services-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/media-services-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/media-services-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/media-services-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/media-services-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/media-services-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/media-services-dynamic-manifest-overview/media-services-skiing.png
