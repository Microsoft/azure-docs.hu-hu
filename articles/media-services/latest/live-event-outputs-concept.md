---
title: Élő események és élő kimenetek – fogalmak
description: Ez a témakör áttekintést nyújt a 3-as Azure Media Services élő eseményeiről és kimeneteiről.
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
ms.date: 10/23/2020
ms.author: inhenkel
ms.openlocfilehash: 44ab9e4ff83fec2ddfbd1cb44f503298d12789d1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766298"
---
# <a name="live-events-and-live-outputs-in-media-services"></a>Élő események és élő kimenetek a Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services lehetővé teszi, hogy élő eseményeket kézbesítsen ügyfeleinek az Azure-felhőben. Ahhoz, hogy élő streamelési eseményeket hoz létre Media Services v3-ban, meg kell értenie a cikkben tárgyalt fogalmakat.

> [!TIP]
> A 2-es Media Services API-kból minkulált ügyfelek esetében az élő  eseményentitás a **Channel** in v2 helyére, az élő kimenet pedig a **programot váltja fel.** 

## <a name="live-events"></a>Élő események

[Az élő események](/rest/api/media/liveevents) feladata az élő videó-hírcsatornák feldolgozása és feldolgozása. Élő esemény létrehozásakor létrejön egy elsődleges és egy másodlagos bemeneti végpont, amely segítségével élő jelet küldhet egy távoli kódolóból. A távoli élő kódoló a bemeneti végpontra küldi a bemeneti csatornát az [RTMP](https://www.adobe.com/devnet/rtmp.html) vagy [Smooth Streaming](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251) (darabolt MP4) bemeneti protokoll használatával. Az RTMP ingest protokoll esetén a tartalmat titkosítva ( ) vagy biztonságosan, a wire( ) protokollon lehet `rtmp://` `rtmps://` elküldeni. A Smooth Streaming protokoll esetében a támogatott URL-sémák a `http://` vagy `https://` a .  

## <a name="live-event-types"></a>Élő eseménytípusok

Az [élő események](/rest/api/media/liveevents) beállíthatók  átmenő (egy helyszíni élő kódoló többféle bitre ható streamet küld) vagy élő kódolásra *(egy* helyszíni élő kódoló egyetlen adatfolyamot küld). A típusok a [LiveEventEncodingType](/rest/api/media/liveevents/create#liveeventencodingtype)használatával való létrehozás során vannak beállítva:

* **LiveEventEncodingType.None:** Egy helyszíni élő kódoló több bites streamet küld. A betöltött stream további feldolgozás nélkül halad át az élő eseményen. Más néven átmenő mód.
* **LiveEventEncodingType.Standard:** Egy helyszíni élő kódoló egyetlen adatfolyamot küld az élő eseménynek, és Media Services hoz létre több bitre ható streamet. Ha a közreműködési csatorna felbontása 720p vagy nagyobb, a **Default720p** előre beállított kódolja a 6 felbontású/bitrates párokat.
* **LiveEventEncodingType.Premium1080p:** Egy helyszíni élő kódoló egyetlen adatfolyamot küld az élő eseménynek, és Media Services több bitre ható streamet hoz létre. A Default1080p beállításkészlet a felbontás/bitrate párok kimeneti készletét határozza meg.

### <a name="pass-through"></a>Továbbítás

![átmenő élő esemény Media Services diagrammal](./media/live-streaming/pass-through.svg)

Az átmenő élő esemény használata esetén a helyszíni élő kódolóra támaszkodva többféle bitre ható videóstreamet hozhat létre, és elküldheti azt az élő eseményhez a közreműködési hírcsatornáként (RTMP vagy töredezett MP4 protokoll használatával). Az élő esemény ezután további feldolgozás nélkül továbbítja a bejövő videóstreameket. Az ilyen átmenő élő események hosszú ideig futó élő eseményekhez vagy 24x365 lineáris élő streameléshez vannak optimalizálva. Ilyen típusú élő esemény létrehozásakor adja meg a Nincs (LiveEventEncodingType.None) értéket.

A bemeneti adatokat legfeljebb 4K felbontással és 60 képkocka/másodperc képkockasebességgel továbbíthatja, H.264/AVC vagy H.265/HEVC videokodekkel és AAC (AAC-LC, HE-AACv1 vagy HE-AACv2) hangkodekkel. További információ: Élő [eseménytípusok összehasonlítása.](live-event-types-comparison-reference.md)

> [!NOTE]
> Az átmenő módszer használata a leggazdaságosabb módja az élő streamelésnek, ha hosszú idő alatt több eseményt is közvetít, és már fektetett helyszíni kódolókba. Tekintse meg [a díjszabás részleteit.](https://azure.microsoft.com/pricing/details/media-services/)
>

Tekintse meg a .NET-kód példáját, amely átmenő élő eseményt hoz létre élő eseményben a [DVR-sel.](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/4a436376e77bad57d6cbfdc02d7df6c615334574/Live/LiveEventWithDVR/Program.cs#L214)

### <a name="live-encoding"></a>Live Encoding  

![élő kódolás Media Services diagrammal](./media/live-streaming/live-encoding.svg)

Ha élő kódolást használ Media Services használatával, úgy konfigurálja a helyszíni élő kódolót, hogy egy egy bites videót küldjön az élő eseményhez a közreműködési hírcsatornaként (RTMP vagy Fragmented-Mp4 protokoll használatával). Ezután beállít egy élő eseményt, hogy az kódolja [ezt](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)a bejövő egy bites streamet egy többféle bitre ható videóstreambe, és elérhetővé teszi a kimenetet az eszközök visszajátszásához olyan protokollok használatával, mint az MPEG-DASH, a HLS és a Smooth Streaming.

Élő kódolás használata esetén a közreműködési hírcsatornát csak legfeljebb 1080p felbontásban küldheti el 30 képkocka/másodperc képkocka sebességgel, H.264/AVC videokodekkel és AAC (AAC-LC, HE-AACv1 vagy HE-AACv2) hangkodekkel. Vegye figyelembe, hogy az átmenő élő események legfeljebb 4 ezer, 60 képkocka/másodperces felbontást támogatnak. További információ: Élő [eseménytípusok összehasonlítása.](live-event-types-comparison-reference.md)

Az élő kódoló kimenetében található felbontásokat és bit értékeket az előzetes beállítás határozza meg. Ha **Standard** élő kódolót használ (LiveEventEncodingType.Standard), akkor a *Default720p* beállításkészlet hat felbontás/bit aránypárból áll, amely 720p 3,5 Mbps-ről 192p-re, 200 kbps-re van állítva. Ellenkező esetben, ha **Premium1080p** élő kódolót használ (LiveEventEncodingType.Premium1080p), akkor a *Default1080p* beállításkészlet hat felbontás/bit sebesség párt határoz meg, amely 1080p 3,5 Mbps-ről 200 kbps-re 180p-re van állítva. További információkat a [rendszerbeállítás-készletekkel](live-event-types-comparison-reference.md#system-presets) kapcsolatos szakaszban talál.

> [!NOTE]
> Ha testre kell szabni az élő kódolás előzetes beállítását, nyisson meg egy támogatási jegyet a Azure Portal. Adja meg a kívánt feloldási és bit-értéktáblát. Ellenőrizze, hogy csak egy réteg 720p-es (ha standard élő kódolóhoz kér előzetes beállítást) vagy 1080p (ha prémium1080p élő kódoló előzetes beállítását kéri), és 6 réteget.

## <a name="creating-live-events"></a>Élő események létrehozása

### <a name="options"></a>Beállítások

Élő esemény létrehozásakor a következő beállításokat adhatja meg:

* Az élő eseménynek nevet és leírást is adhat.
* A felhőalapú kódolás magában foglalja az átmenő (felhőalapú kódolás nélkül), a Standard (legfeljebb 720p) vagy a Prémium (legfeljebb 1080p) típust. A Standard és a Premium kódoláshoz kiválaszthatja a kódolt videó többszakaszos módját.
  * Nincs: Szigorúan figyelembe véve a kódolási beállításkészletben megadott kimeneti felbontást anélkül, hogy figyelembe vesszük a bemeneti videó képpontos oldalarányát vagy megjelenítési oldalarányát.
  * Automatikus méretezés: Felülbírálja a kimeneti felbontást, és úgy módosítja, hogy megfeleljen a bemenet megjelenítési oldalarányának kitöltés nélkül. Ha például a bemenet 1920x1080, és a kódolási beállítás 1280x1280 értéket kér, akkor az előre beállított érték felül lesz bírálva, a kimenet pedig 1280x720, amely a 16:9-es bemeneti oldalarányt tartja fenn.
  * AutoFit: A kimenetet (letterbox vagy pillar box) kitöltésével biztosítja a kimenet felbontásának betartásához, ugyanakkor biztosítja, hogy a kimenet aktív videó régiója az oldalarányával azonos a bemenetével. Ha például a bemenet 1920x1080, és a kódolási beállítás 1280x1280-at kér, akkor a kimenet 1280x1280 lesz, amely egy 1280x720-as belső téglalapot tartalmaz 16:9 oldalarányú méretarányban, bal és jobb oldalon pedig 280 képpont széles alapszögekkel.
* Streamelési protokoll (jelenleg az RTMP és Smooth Streaming protokollok támogatottak). A protokoll beállítását nem módosíthatja, amíg az élő esemény vagy a hozzá tartozó élő kimenetek futnak. Ha különböző protokollokra van szüksége, hozzon létre külön élő eseményt minden streamelési protokollhoz.
* Bemeneti azonosító, amely az élő esemény bemeneti streamje globálisan egyedi azonosítója.
* Statikus gazdagépnév előtagja, amely egyiket sem tartalmazza (ebben az esetben egy véletlenszerű, 128 bites hexikai sztringet fog használni), Élő eseménynév használata vagy Egyéni név használata.  Ha ügyfélnevet választ, ez az érték az Egyéni állomásnév előtag.
* Az élő közvetítés és a lejátszás közötti végpontok közötti késés csökkenthető a bemeneti kulcs képkocka-intervallumának beállításával, amely a HLS-kimenet egyes médiaszegmensének időtartama (másodpercben). Az értéknek nullától különböző egész számnak kell lennie 0,5 és 20 másodperc között.  Az alapértelmezett érték 2  másodperc, ha egyik bemeneti vagy kimeneti kulcs képkocka-intervalluma sincs beállítva. A kulcskeret időköze csak átmenő események esetén engedélyezett.
* Az esemény létrehozásakor beállíthatja az automatikus indítást. Ha az automatikus indítás true (igaz) értékre van állítva, az élő esemény a létrehozás után elindul. A számlázás az élő esemény futásának kezdetekor kezdődik. A további számlázás leállításához explicit módon meg kell hívnia a Stop (Leállítás) hívást az élő esemény erőforrásán. Másik lehetőségként elindíthatja az eseményt, amikor készen áll a streamelésre.

> [!NOTE]
> A maximális képkocka-érték Standard és Prémium kódolás esetén is 30 mp.

## <a name="standby-mode"></a>Készenléti mód

Élő esemény létrehozásakor beállíthatja Azt Készenléti módra. Amíg az esemény Készenléti módban van, szerkesztheti a Leírás és a Statikus állomásnév előtagot, valamint korlátozhatja a bemeneti és az előnézeti hozzáférési beállításokat.  A Készenléti mód továbbra is számlázható mód, de az ára eltér az élő streamek elindítanitól.

További információ: Élő [esemény államok és számlázás.](live-event-states-billing-concept.md)

* IP-korlátozások a betöltési és az előnézeti címen. Megadhatja azokat az IP-címeket, amelyek számára engedélyezett a videók élő eseményhez való behívása. Az engedélyezett IP-címek köre tartalmazhat egyetlen IP-címet (például „10.0.0.1”), vagy egy IP-tartományt, amelyet egy IP-cím és egy CIDR alhálózati maszk (például„10.0.0.1/22”) vagy egy IP-cím és egy pontozott decimális alhálózati maszk (például „10.0.0.1(255.255.252.0)”) segítségével lehet megadni.
<br/><br/>
Ha nem ad meg IP-címet, és nincs szabálydefiníció, akkor az IP-cím nem lesz engedélyezve. Ha az összes IP-címnek szeretne engedélyt adni, hozzon létre egy szabályt, és állítsa be a következő értéket: 0.0.0.0/0.<br/>Az IP-címeknek a következő formátumok egyikében kell lennie: IpV4-cím négy számmal vagy CIDR-címtartománysal.
<br/><br/>
Ha engedélyezni szeretne bizonyos IP-címeket a saját tűzfalán, vagy az élő események bemeneteit Azure IP-címekre szeretné korlátozni, töltsön le egy JSON-fájlt az [Azure Datacenter IP-címtartományaiból.](https://www.microsoft.com/download/details.aspx?id=41653) A fájl részleteiért válassza a **Részletek** szakaszt az oldalon.

* Az esemény létrehozásakor bekapcsolhatja az élő átiratokat. Alapértelmezés szerint az élő átírás le van tiltva. További információ az élő átírásról: [Élő átírás.](live-event-live-transcription-how-to.md)

### <a name="naming-rules"></a>Elnevezési szabályok

* Az élő események maximális neve 32 karakter.
* A névnek a következő [regex mintát kell követnie:](/dotnet/standard/base-types/regular-expression-language-quick-reference) `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$` .

Lásd még [a streamvégpont elnevezési konvencióit.](stream-streaming-endpoint-concept.md#naming-convention)

> [!TIP]
> Az élő esemény nevének egyediségét úgy garantálhatja, hogy létrehoz egy GUID azonosítót, majd eltávolítja az összes kötőjelet és kötőjelet (ha van). A sztring minden élő eseményben egyedi lesz, és a hossza garantáltan 32 lesz.

## <a name="live-event-ingest-urls"></a>Élő eseménybetöltési URL-címek

Az élő esemény létrehozása után lekért url-címeket az élő helyszíni kódolónak. Az élő kódoló ezekre az URL-címekre küldi a bemeneti élő streamet. További információ: [Ajánlott helyszíni élő kódolók.](encode-recommended-on-premises-live-encoders.md)

>[!NOTE]
> A 2020-05-01-es API-kiadástól a "hamis" URL-címeket statikus gazdagépneveknek (useStaticHostname: true) nevezik.


> [!NOTE]
> Ahhoz, hogy egy bemenő URL-cím statikus és kiszámítható legyen a hardveres kódolók beállításaiban való használathoz, állítsa a **useStaticHostname** tulajdonságot true (igaz) értékre, és minden létrehozáskor állítsa az **accessToken** tulajdonságot azonos GUID azonosítóra. 

### <a name="example-liveevent-and-liveeventinput-configuration-settings-for-a-static-non-random-ingest-rtmp-url"></a>Példa a LiveEvent és a LiveEventInput konfigurációs beállításokra egy statikus (nem véletlenszerű) RTMP URL-címbe való beúszás esetén.

```csharp
             LiveEvent liveEvent = new LiveEvent(
                    location: mediaService.Location,
                    description: "Sample LiveEvent from .NET SDK sample",
                    // Set useStaticHostname to true to make the ingest and preview URL host name the same. 
                    // This can slow things down a bit. 
                    useStaticHostname: true,

                    // 1) Set up the input settings for the Live event...
                    input: new LiveEventInput(
                        streamingProtocol: LiveEventInputProtocol.RTMP,  // options are RTMP or Smooth Streaming ingest format.
                                                                         // This sets a static access token for use on the ingest path. 
                                                                         // Combining this with useStaticHostname:true will give you the same ingest URL on every creation.
                                                                         // This is helpful when you only want to enter the URL into a single encoder one time for this Live Event name
                        accessToken: "acf7b6ef-8a37-425f-b8fc-51c2d6a5a86a",  // Use this value when you want to make sure the ingest URL is static and always the same. If omitted, the service will generate a random GUID value.
                        accessControl: liveEventInputAccess, // controls the IP restriction for the source encoder.
                        keyFrameIntervalDuration: "PT2S" // Set this to match the ingest encoder's settings
                    ),
```

* Nem statikus állomásnév

    A nem statikus gazdagépnév a 3-as Media Services alapértelmezett módja a **LiveEvent létrehozásakor.** Az élő eseményt valamivel gyorsabban lefoglalhatja, de az élő kódolási hardverhez vagy szoftverhez szükséges bemenő URL-cím véletlenszerű lesz. Az URL-cím megváltozik, ha leállítja/elindítja az élő eseményt. A nem statikus gazdanevek csak olyan esetekben hasznosak, amikor a végfelhasználó olyan alkalmazással szeretne streamet streamelni, amelynek nagyon gyorsan kell élő eseményt kapnia, és a dinamikus bemenő URL-cím használata nem jelent problémát.

    Ha egy ügyfélalkalmazásnak nem kell előre létrehoznia egy bemenő URL-címet az élő esemény létrehozása előtt, hagyja, hogy Media Services automatikusan generálja az élő esemény hozzáférési jogkivonatát.

* Statikus gazdagépnevek 

    A statikus gazdanév módot a legtöbb operátor részesíti előnyben, akik előre szeretnék konfigurálni az élő kódolási hardverüket vagy szoftvereiket egy RTMP-bemenő URL-cím használatával, amely soha nem változik meg egy adott élő esemény létrehozásakor, illetve leállításakor/kezdetekor. Ezek az operátorok egy prediktív RTMP-bemenő URL-címet szeretne, amely az idő előre nem változik. Ez akkor is nagyon hasznos, ha statikus RTMP-bemenő URL-címet kell leküldetni egy hardverkódoló eszköz konfigurációs beállításaiba, például a BlackMagic Atem Mini Pro vagy hasonló hardverkódoló és éles eszközök konfigurációs beállításaiba. 

    > [!NOTE]
    > A Azure Portal statikus állomásnév URL-címének neve "*Static hostname prefix*".

    Ennek a módnak az API-ban való megadásához a beállítást állítsa a `useStaticHostName` `true` következőre a létrehozáskor: (az alapértelmezett érték `false` ). Ha a true (igaz) értékre van állítva, a megadja az élő esemény előnézete és a bemenő végpontok gazdagépnevének `useStaticHostname` `hostnamePrefix` első részét. A végső állomásnév ennek az előtagnak, a Media Service-fiók nevének és az adatközpont rövid kódának Azure Media Services lehet.

    Ha el kell kerülnie egy véletlenszerű jogkivonatot az URL-címben, a létrehozáskor át kell adnia a saját hozzáférési `LiveEventInput.accessToken` jogkivonatát ().  A hozzáférési jogkivonatnak érvényes GUID sztringnek kell lennie (kötőjelekkel vagy anélkül). Ha a mód be van állítva, nem frissíthető.

    A hozzáférési jogkivonatnak egyedinek kell lennie az Azure-régióban és Media Services fiókjában. Ha az alkalmazásnak statikus állomásnév-bemenő URL-címet kell használnia, javasoljuk, hogy mindig hozzon létre új GUID-példányt a régió, a Media Services-fiók és az élő esemény egy adott kombinációjával való használathoz.

    A következő API-k használatával engedélyezheti a statikus állomásnév URL-címét, és beállíthatja a hozzáférési jogkivonatot egy érvényes GUID azonosítóra `"accessToken": "1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"` (például).  

    |Nyelv|Statikus állomásnév URL-címének engedélyezése|Hozzáférési jogkivonat beállítása|
    |---|---|---|
    |REST|[properties.useStaticHostname](/rest/api/media/liveevents/create#liveevent)|[LiveEventInput.useStaticHostname](/rest/api/media/liveevents/create#liveeventinput)|
    |parancssori felület|[--use-static-hostname](/cli/azure/ams/live-event#az_ams_live_event_create)|[--access-token](/cli/azure/ams/live-event#optional-parameters)|
    |.NET|[LiveEvent.useStaticHostname](/dotnet/api/microsoft.azure.management.media.models.liveevent.usestatichostname?view=azure-dotnet&preserve-view=true#Microsoft_Azure_Management_Media_Models_LiveEvent_UseStaticHostname)|[LiveEventInput.AccessToken](/dotnet/api/microsoft.azure.management.media.models.liveeventinput.accesstoken#Microsoft_Azure_Management_Media_Models_LiveEventInput_AccessToken)|

### <a name="live-ingest-url-naming-rules"></a>Élő bemenő URL-címek elnevezési szabályai

* Az alábbi *véletlenszerű* sztring egy 128 bites hexadecimális szám (amely 32 karakterből áll 0-9-ig és a-f-ig).
* *saját hozzáférési jogkivonat:* A statikus gazdagépnév beállítás használata esetén beállított érvényes GUID-sztring. Például: `"1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`.
* *stream neve:* Egy adott kapcsolat streamnevét jelzi. A stream nevének értékét általában a használt élő kódoló ad hozzá. Az élő kódolót beállíthatja úgy, hogy bármilyen nevet használjon a kapcsolat leírásához, például: "video1_audio1", "video2_audio1", "stream".

#### <a name="non-static-hostname-ingest-url"></a>Nem statikus gazdanév bemenő URL-címe

##### <a name="rtmp"></a>RTMP

`rtmp://<random 128bit hex string>.channel.media.azure.net:1935/live/<auto-generated access token>/<stream name>`<br/>
`rtmp://<random 128bit hex string>.channel.media.azure.net:1936/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2935/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2936/live/<auto-generated access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Zökkenőmentes streamelés

`http://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>

#### <a name="static-hostname-ingest-url"></a>Statikus állomásnév bemenő URL-címe

A következő elérési utakon a az eseménynek megadott nevet vagy az élő esemény létrehozásakor használt egyéni `<live-event-name>` nevet jelenti.

##### <a name="rtmp"></a>RTMP

`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1935/live/<your access token>/<stream name>`<br/>
`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1936/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2935/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2936/live/<your access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Zökkenőmentes streamelés

`http://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>

## <a name="live-event-preview-url"></a>Élő esemény előnézetének URL-címe

Ha az élő esemény elkezdi megkapni a közreműködési hírcsatornát, a hozzá előzetes verziójú végpont használatával megtekintheti és ellenőrizheti, hogy megkapja-e az élő streamet a további közzététel előtt. Miután ellenőrizte, hogy az előnézeti stream megfelelő-e, az élő esemény használatával elérhetővé teszi az élő streamet egy vagy több (előre létrehozott) streamvégponton keresztül. Ehhez hozzon létre egy új [élő kimenetet](/rest/api/media/liveoutputs) az élő eseményen.

> [!IMPORTANT]
> A folytatás előtt győződjön meg arról, hogy a videó az előnézeti URL-címre áramlik.

## <a name="live-event-long-running-operations"></a>Hosszú ideig futó élő események műveletei

Részletekért lásd a [hosszú ideig futó műveleteket.](media-services-apis-overview.md#long-running-operations)

## <a name="live-outputs"></a>Élő kimenetek

Miután az élő eseménybe áramlott a stream, megkezdheti [a](/rest/api/media/assets)streamelési eseményt egy [eszköz,](/rest/api/media/liveoutputs)egy élő kimenet és egy [streamelési lokátor létrehozásával.](/rest/api/media/streaminglocators) Az élő kimenet archiválja a streamet, és elérhetővé teszi a nézők számára a [streamvégponton keresztül.](/rest/api/media/streamingendpoints)  

Az élő kimenetekkel kapcsolatos részletes információkért [lásd: Using a cloud DVR (Felhőalapú DVR használata).](live-event-cloud-dvr-time-how-to.md)

## <a name="live-event-output-questions"></a>Élő esemény kimenetének kérdései

Tekintse meg az [élő események kimenetére kapcsolatos kérdéseket.](questions-collection.md#live-streaming)
