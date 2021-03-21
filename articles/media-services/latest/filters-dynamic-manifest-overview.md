---
title: A jegyzékfájlok szűrése dinamikus csomagoló használatával
description: Ismerje meg, hogyan hozhat létre szűrőket dinamikus csomagoló használatával a jegyzékfájlok szűréséhez és szelektív továbbításához.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 3ffdb41752630e0e5e22303ff58ecd798595a890
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98897662"
---
# <a name="filter-your-manifests-using-dynamic-packager"></a>A jegyzékfájlok szűrése dinamikus csomagoló használatával

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ha adaptív sávszélességű adatfolyam-továbbítási tartalmat továbbít az eszközökhöz, előfordulhat, hogy egy jegyzékfájl több verzióját kell közzétennie az adott eszköz képességeinek vagy a rendelkezésre álló hálózati sávszélességnek a megcélzásához. A [dinamikus](dynamic-packaging-overview.md) csomagolás lehetővé teszi olyan szűrők megadását, amelyek meghatározott kodekeket, felbontásokat, bitrátákat és hangsávok kombinációkat végezhetnek el menet közben. Ez a szűrés nem szükséges több másolat létrehozásához. Egyszerűen közzé kell tennie egy új URL-címet a célként megadott eszközökhöz konfigurált szűrők (iOS, Android, Okostelevízió üzletága vagy böngészők) és a hálózati képességek (nagy sávszélességű, mobil vagy alacsony sávszélességű forgatókönyvek) számára. Ebben az esetben az ügyfelek a lekérdezési karakterláncon keresztül kezelhetik a tartalom folyamatos átvitelét (az elérhető [eszközcsoport-szűrők vagy-szűrők](filters-concept.md)megadásával), és szűrők használatával továbbítják a stream adott részeit.

Bizonyos kézbesítési forgatókönyvek esetében meg kell győződnie arról, hogy az ügyfél nem fér hozzá bizonyos műsorszámokhoz. Előfordulhat például, hogy nem szeretne közzétenni egy olyan jegyzékfájlt, amely HD-számokat tartalmaz egy adott előfizetői szinten. Vagy előfordulhat, hogy el kívánja távolítani a speciális adaptív sávszélességű (ABR) útvonalakat, hogy csökkentse a szállítási költségeket egy adott eszközre, amely nem használja a további számokat. Ebben az esetben a létrehozáskor társíthatja az előre létrehozott szűrők listáját a [folyamatos átviteli lokátorhoz](streaming-locators-concept.md) . Az ügyfelek ezután nem kezelhetik a tartalom adatfolyamként való továbbítását, mivel azt a **folyamatos átviteli lokátor** határozza meg.

A szűrést kombinálhatja a szűrők megadásával [a folyamatos átviteli lokátorban](filters-concept.md#associating-filters-with-streaming-locator) + további, az ügyfél által az URL-ben megadott szűrőket is. Ez a kombináció hasznos lehet a további sávok, például a metaadatok, az esemény-adatfolyamok, a hangnyelvek és a leíró hangsávok korlátozására.

A stream különböző szűrőinek megadásának lehetősége hatékony **dinamikus jegyzékfájl** -manipulációs megoldást biztosít a megcélzott eszközök több használati esetének megcélzásához. Ez a témakör a **dinamikus jegyzékekkel** kapcsolatos fogalmakat ismerteti, és példákat mutat be azokra a forgatókönyvekre, amelyekben használhatja ezt a funkciót.

> [!NOTE]
> A dinamikus jegyzékfájlok nem változtatják meg az adott eszközre vonatkozó adategységet és az alapértelmezett jegyzékfájlt.

## <a name="overview-of-manifests"></a>A jegyzékfájlok áttekintése

Azure Media Services támogatja a HLS, az MPEG DASH és a Smooth Streaming protokollokat. A [dinamikus csomagolás](dynamic-packaging-overview.md)részeként a folyamatos átviteli ügyfél-jegyzékfájlok (HLS Master Playlist, Dash Media Presentation description [mpd] és Smooth streaming) dinamikusan jönnek létre az URL-cím kiválasztó alapján. További információ: a kézbesítési protokollok az [általános igény szerinti munkafolyamatban](dynamic-packaging-overview.md#to-prepare-your-source-files-for-delivery).

### <a name="get-and-examine-manifest-files"></a>Manifest-fájlok beolvasása és vizsgálata

A szűrő nyomon követése tulajdonság feltételeinek listáját kell megadnia, amely alapján a stream (élő vagy video on-demand [VOD]) nyomon követését egy dinamikusan létrehozott jegyzékfájlba kell foglalni. A zeneszámok tulajdonságainak megszerzéséhez és vizsgálatához először be kell töltenie a Smooth Streaming jegyzékfájlt.

A [fájlok feltöltése, kódolása és továbbítása a .net](stream-files-tutorial-with-api.md#get-streaming-urls) -oktatóanyagmal bemutatja, hogyan hozhatja létre a streaming URL-címeket a .net-tel. Ha futtatja az alkalmazást, az egyik URL-cím a Smooth Streaming jegyzékfájlra mutat: `https://amsaccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-0000000000000/ignite.ism/manifest` .<br/> Másolja és illessze be az URL-címet egy böngésző címsorába. A rendszer letölti a fájlt. Bármely szövegszerkesztőben megnyithatja.

A REST-példákat lásd: [fájlok feltöltése, kódolása és továbbítása a REST](stream-files-tutorial-with-rest.md#list-paths-and-build-streaming-urls)-tel.

### <a name="monitor-the-bitrate-of-a-video-stream"></a>Videó stream bitrátájának figyelése

A videó streamek bitrátájának figyeléséhez használhatja a [Azure Media Player bemutató oldalt](https://aka.ms/azuremediaplayer) . A bemutató oldal diagnosztikai adatokat jelenít meg a **diagnosztika** lapon:

![Azure Media Player diagnosztika][amp_diagnostics]

### <a name="examples-urls-with-filters-in-query-string"></a>Példák: a lekérdezési karakterláncban szűrőket tartalmazó URL-címek

Szűrőket alkalmazhat az ABR streaming protokollokra: HLS, MPEG-DASH és Smooth Streaming. Az alábbi táblázat néhány példát mutat be a szűrőket tartalmazó URL-címekre:

|Protokoll|Példa|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="rendition-filtering"></a>Kiadatás szűrése

Dönthet úgy is, hogy több kódolási profilba kódolja az eszközt (H. 264 alapkonfiguráció, H. 264 magas, AACL, AACH, Dolby Digital Plus) és több minőségi bitráta. Azonban nem minden ügyféleszközök fogja támogatni az összes eszköz profilját és bitrátáját. A régebbi Android-eszközök például csak a H. 264 Baseline + AACL támogatják. Nagyobb bitsebességek küldése olyan eszközre, amely nem tudja beolvasni az előnyöket a sávszélesség és az eszközök számítási feladatokhoz. Az eszköznek az összes megadott információt dekódolnia kell, csak a megjelenítéshez.

A dinamikus jegyzékfájl használatával létrehozhat olyan eszközöket (például mobil, konzol vagy HD/SD), amelyek az egyes profilok részét képező zeneszámokat és tulajdonságokat tartalmazzák. Ezt nevezzük a kiadatás szűrésének. Az alábbi ábrán egy példa látható.

![Példa a dinamikus jegyzékfájlt tartalmazó kiadatási szűrésre][renditions2]

A következő példában egy kódolót használtak egy köztes eszköz kódolásához hét ISO MP4 (180p és 1080p között). A kódolt eszköz [dinamikusan becsomagolható](dynamic-packaging-overview.md) a következő streaming protokollok bármelyikére: HLS, MPEG Dash és Smooth.

A következő ábrán látható felső rész az objektum HLS-jegyzékét mutatja szűrő nélkül. (Ez mind a hét összes kiadatását tartalmazza.)  A bal alsó részen az ábrán egy HLS-jegyzék látható, amelybe az "ott" nevű szűrő lett alkalmazva. Az "ott" szűrő határozza meg az 1 Mbps alatti összes bitrátát, így a válaszban az alsó két minőségi szint le lett eltávolítva. A jobb alsó sarokban a diagram azt a HLS-jegyzékfájlt jeleníti meg, amelybe a "Mobile" nevű szűrő lett alkalmazva. A "mobil" szűrő határozza meg a leképezések eltávolítását, ahol a felbontás nagyobb a 720p-nál, így a két 1080p-leküldés le lett szakítva.

![Kiadatási szűrés dinamikus Jegyzékfájlval][renditions1]

## <a name="removing-language-tracks"></a>Nyelvi nyomon követések eltávolítása
Az eszközök több hangnyelvt is tartalmazhatnak, például angol, spanyol, francia stb. Általában a Player SDK kezeli az alapértelmezett hangsáv-kiválasztási és elérhető hangsávokat felhasználónként kiválasztva.

Az ilyen játékos SDK-k fejlesztése kihívást jelent, mivel különböző implementációkat igényel az eszközre jellemző lejátszó keretrendszerek között. Emellett bizonyos platformokon a lejátszó API-k korlátozottak, és nem tartalmazzák a hangkijelölési funkciót, ahol a felhasználók nem választhatják ki vagy nem változtathatják meg az alapértelmezett hangsávot. Az Asset Filters használatával szabályozhatja a viselkedést olyan szűrők létrehozásával, amelyek csak a kívánt hangnyelveket tartalmazzák.

![A nyelvi sávok szűrése dinamikus jegyzékkel][language_filter]

## <a name="trimming-the-start-of-an-asset"></a>Egy eszköz kezdetének vágása

A legtöbb élő közvetítési eseménynél a kezelők a tényleges esemény előtt futtassanak teszteket. Például tartalmazhatnak egy, az eseményt megelőzően megjelenő előlappal: "a program hamarosan elindul."

Ha a program archiválást tartalmaz, a rendszer archiválja a teszt-és az adattárat, és tartalmazza a bemutatót is. Ezek az információk azonban nem jelennek meg az ügyfelek számára. A dinamikus jegyzékfájl használatával létrehozhat egy kezdési időpontot, és eltávolíthatja a nemkívánatos adatokból a jegyzékfájlt.

![Egy eszköz kivágása dinamikus Jegyzékfájlsal][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>Alklipek (nézetek) létrehozása élő archívumból

Sok élő esemény hosszú ideig fut, és az élő archiválás több eseményt is tartalmazhat. Az élő esemény vége után a műsorszolgáltatók érdemes lehet az élő archívumot feltörni a logikai programok indítására és leállítására.

Ezeket a virtuális programokat külön teheti közzé az élő Archívum feldolgozása nélkül, és nem hozhat létre külön objektumokat (ez nem teszi elérhetővé a meglévő gyorsítótárazott töredékek előnyeit a CDNs). Ilyen virtuális programok például a labdarúgás vagy kosárlabda játékának, a baseball inning-nek vagy bármely sportesemény egyes eseményeinek a negyedévei.

A dinamikus jegyzékfájl használatával szűrőket hozhat létre a kezdési és befejezési időpontok segítségével, és virtuális nézeteket hozhat létre az élő Archívum tetején.

![Alklip szűrő dinamikus Jegyzékfájlval][subclip_filter]

Itt látható a szűrt eszköz:

![Szűrt eszköz dinamikus Jegyzékfájlval][skiing]

## <a name="adjusting-the-presentation-window-dvr"></a>A bemutató ablak módosítása (DVR)

A Azure Media Services jelenleg körkörös archívumot biztosít, ahol az időtartam 1 perc és 25 óra között konfigurálható. A jegyzékfájlok szűrésével az Archívum felső részén, az adathordozó törlése nélkül hozhat létre egy gördülő DVR-ablakot. A műsorszolgáltatók számos szituációban szeretnék biztosítani, hogy az élő Edge-vel való áthelyezés és a nagyobb archiválási időszak megtartása mellett egy korlátozott DVR-ablakot is biztosítson. A műsorszolgáltató érdemes lehet a DVR ablakból kiemelni a klipek kiemelésére szolgáló, vagy különböző DVR-ablakokat különböző eszközök számára. Előfordulhat például, hogy a mobileszközök többsége nem kezeli a nagyméretű DVR-ablakokat (a mobileszközök esetében 2 perces DVR-ablakot és asztali ügyfelek számára egy órát is igénybe vehet).

![DVR-ablak dinamikus Jegyzékfájlval][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>LiveBackoff módosítása (élő pozíció)

A jegyzékfájl szűrésével több másodpercet lehet eltávolítani egy élő program élő szélétől. A szűrés lehetővé teszi a műsorszolgáltatók számára, hogy megtekintsék a bemutatót az előzetes verziójú közzétételi ponton, és hirdetmények beszúrási pontjait hozzanak létre, mielőtt a nézők megkapják a streamet (a biztonsági mentés 30 másodperctel A műsorszolgáltatók ezután leküldhetik ezeket a hirdetéseket az ügyfél-keretrendszerekbe, hogy a hirdetmények megkezdése előtt megkapják és feldolgozzák az információkat.

A hirdetmények támogatása mellett az élő visszatartási beállítással is beállíthatja a megjelenítők pozícióját, így amikor az ügyfelek elsodródnak és az élő Edge-re kerülnek, továbbra is kaphatnak töredékeket a kiszolgálóról. Így az ügyfelek nem kapnak HTTP 404 vagy 412 hibát.

![Szűrés az élő visszalépéshez dinamikus jegyzékfájl alapján][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Több szabály kombinálása egyetlen szűrőben

Egyetlen szűrőben több szűrési szabályt is egyesítheti. Megadhat például egy "tartományra vonatkozó szabályt", amellyel eltávolíthatja az élő archívumból származó beadásokat, és kiszűrheti a rendelkezésre álló bitrátákat is. Több szűrési szabály alkalmazása esetén a végeredmény az összes szabály metszete.

![Több szűrési szabály dinamikus Jegyzékfájlval][multiple-rules]

## <a name="combining-multiple-filters-filter-composition"></a>Több szűrő egyesítése (szűrő összetétele)

Egyetlen URL-címben több szűrőt is egyesíthet. Az alábbi forgatókönyv azt mutatja be, hogy miért érdemes a szűrőket egyesíteni:

1. A videók minőségének korlátozásához szűrést kell végeznie a mobileszközök, például az Android vagy az iPad számára. A nemkívánatos tulajdonságok eltávolításához létre kell hoznia egy, az eszköz profiljaihoz megfelelő fióknevet. A fiókhoz tartozó szűrőket a Media Services fiókban lévő összes eszközhöz használhatja további társítás nélkül.
1. Egy eszköz kezdési és befejezési időpontját is el szeretné metszeni. A vágás végrehajtásához létre kell hoznia egy adategység szűrőt, és be kell állítania a kezdő/záró időpontot.
1. Mindkét szűrőt össze szeretné kapcsolni. A kombináció nélkül hozzá kell adnia a minőségi szűrést a kivágási szűrőhöz, amely nehezebbé tenné a szűrési használatot.

A szűrők összekapcsolásához állítsa a szűrő nevét a jegyzékfájl/lista URL-címére pontosvesszővel tagolt formátumban. Tegyük fel, hogy rendelkezik egy *MyMobileDevice* nevű szűrővel, amely a tulajdonságokat szűri, és egy másik nevű *MyStartTime* rendelkezik egy adott kezdési időpont beállításához. Legfeljebb három szűrőt lehet kombinálni.

További információt [ebben a blogbejegyzésben](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/)talál.

## <a name="considerations-and-limitations"></a>Megfontolandó szempontok és korlátozások

- A **forceEndTimestamp**, a **PresentationWindowDuration** és a **liveBackoffDuration** értékeit nem szabad a VOD-szűrőhöz beállítani. Csak élő szűrési helyzetekben használatosak.
- A dinamikus jegyzékfájl a GOP-határokon (kulcstárolókban) működik, ezért a nyírási pontossággal rendelkezik.
- Használhatja ugyanazt a szűrőt a fiók-és az adategység-szűrőkhöz. Az eszközök szűrői magasabb prioritással rendelkeznek, és felülbírálják a fiókok szűrőit.
- Ha frissít egy szűrőt, akár 2 percet is igénybe vehet, amíg a streaming végpont frissíti a szabályokat. Ha szűrőket használt a tartalom kiszolgálásához (és gyorsítótárazta a tartalmat a proxykban és a CDN-gyorsítótárban), akkor a szűrők frissítése a lejátszó meghibásodását okozhatja. Javasoljuk, hogy a szűrő frissítése után törölje a gyorsítótárat. Ha ez a beállítás nem lehetséges, érdemes lehet egy másik szűrőt használni.
- Az ügyfeleknek manuálisan kell letölteniük a jegyzékfájlt, és elemezni kell a pontos kezdési időbélyeget és az időskálát.

    - Egy adott eszközön lévő zeneszámok tulajdonságainak meghatározásához [szerezze be és vizsgálja meg a jegyzékfájlt](#get-and-examine-manifest-files).
    - Az objektum-szűrő időbélyegének tulajdonságainak beállítására szolgáló képlet a következő: <br/>startTimestamp = &lt; Kezdési idő a jegyzékfájlban &gt;  +   &lt; várt szűrési kezdési idő másodpercben &gt; * időskála

## <a name="next-steps"></a>Következő lépések

A következő cikkek bemutatják, hogyan hozhatók létre programozott módon a szűrők:  

- [Szűrők létrehozása REST API-kkal](filters-dynamic-manifest-rest-howto.md)
- [Szűrők létrehozása .NET használatával](filters-dynamic-manifest-dotnet-howto.md)
- [Szűrők létrehozása a parancssori felület használatával](filters-dynamic-manifest-cli-howto.md)

[renditions1]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/filters-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/filters-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/filters-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/filters-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/filters-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/filters-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/filters-dynamic-manifest-overview/media-services-skiing.png
[amp_diagnostics]: ./media/filters-dynamic-manifest-overview/amp_diagnostics.png
