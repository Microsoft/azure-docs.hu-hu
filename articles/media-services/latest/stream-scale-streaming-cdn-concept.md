---
# <a name="mandatory-fields-see-more-on-akamsskyeyemeta"></a>Kötelező mezők. További információ: aka.ms/skyeye/meta.
title: tartalom továbbítása a CDN-integrációval: Azure Media Services Leírás: Ismerje meg, hogyan közvetítheti a tartalmat a CDN-integrációval, valamint hogy hogyan kérhető le és Origin-Assist CDN-prefektus.
szolgáltatások: Media-Services documentationcenter: ' ' Author: IngridAtMicrosoft Manager: femila Editor: ' ' MS. Service: Media-Services MS. munkaterhelés: MS. topic: konceptuális MS. Date: 08/31/2020 MS. Author: inhenkel
---

# <a name="stream-content-with-cdn-integration"></a>Tartalom továbbítása CDN-integrációval

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Az Azure tartalomkézbesítési hálózat CDN a tartalmakat világszerte, stratégiai alapon elhelyezett fizikai csomópontokon gyorsítótárazva globális megoldást kínál a fejlesztők számára a tartalmak nagy sávszélességű gyors kézbesítéséhez a felhasználók felé.  

A CDN gyorsítótárba helyezi a tartalmat egy Media Services [streaming Endpoint (forrás)](stream-streaming-endpoint-concept.md) alapján, adatfolyam-protokoll, bitráta, tároló formátum és TITKOSÍTÁS/DRM használatával. A codec-Streaming Protocol-Container Format-bitráta-encryption minden kombinációjára külön CDN-gyorsítótár lesz.

A népszerű tartalmat a rendszer közvetlenül a CDN-gyorsítótárból fogja kiszolgálni, feltéve, hogy a videó töredékét gyorsítótárazza. Az élő tartalom valószínűleg gyorsítótárazva lesz, mert általában sok ember nézi pontosan ugyanazt a dolgot. Az igény szerinti tartalom egy kicsit bonyolultabb lehet, mert olyan tartalmakat is használhat, amelyek népszerűek és nem. Ha több millió videós eszközt is tartalmaz, ahol egyikük sem népszerű (csak egy vagy két nézők hetente), de több ezer ember nézi a különböző videókat, a CDN sokkal kevésbé lesz hatékony.

Azt is meg kell fontolnia, hogyan működik az adaptív adatfolyam. Minden egyes videó-töredék saját entitásként van gyorsítótárazva. Képzelje el például, hogy az első alkalommal figyel egy bizonyos videót. Ha a megjelenítő kihagyja a körülbelül néhány másodpercet, és ott van, csak a videó töredékek vannak társítva a CDN-ben a megfigyelt személyhez. Az adaptív streaming esetében általában 5 – 7 különböző videó-bitrátát használhat. Ha egy személy figyeli az egyik bitrátát, és egy másik személy más bitrátát figyel, akkor a CDN-ben külön-külön vannak gyorsítótárazva. Még akkor is, ha két személy nézi ugyanazt a sávszélességet, különböző protokollokon keresztül lehet adatfolyamként szolgálni. Az egyes protokollokat (HLS, MPEG-DASH, Smooth Streaming) külön gyorsítótárazza a rendszer. Így az egyes bitráták és protokollok külön vannak gyorsítótárazva, és csak a kért videó-töredékek vannak gyorsítótárazva.

A tesztkörnyezet kivételével javasoljuk, hogy a CDN legyen engedélyezve mind a standard, mind a prémium szintű streaming végpontokhoz. A streaming-végpontok minden típusa eltérő támogatott átviteli korláttal rendelkezik.
Az adatfolyam-végpont által támogatott egyidejű adatfolyamok maximális számának pontos kiszámítása nehéz feladat, mivel számos tényezőt figyelembe kell venni. Ezek a következők:

- A folyamatos átvitelhez használt maximális átviteli sebesség
- A lejátszó előpuffere és a váltás viselkedése. A játékosok megpróbálnak kiváltani szegmenseket a forrásokból, és a terhelési sebességet használják az adaptív sávszélesség-váltás kiszámításához. Ha egy folyamatos átviteli végpont a telítettséghez közeledik, a válaszidő változhat, és a játékosok alacsonyabb minőségi értékre válthatnak. Mivel ez csökkenti a streaming Endpoint Player-lejátszók terhelését, a magasabb színvonalú, nem kívánt váltási eseményindítók létrehozásával.
Összességében biztonságos az egyidejű adatfolyamok maximális sebességének becslése a maximális átviteli végpont teljesítményének megadásával, és ezt a maximális sávszélesség (feltéve, hogy minden játékos a legmagasabb sávszélességet használja). Rendelkezhet például egy standard folyamatos átviteli végponttal, amely 600 Mbps-ra és a 3Mbp legmagasabb sebességére van korlátozva. Ebben az esetben a felső bitrátánál körülbelül 200 egyidejű adatfolyam támogatott. Ne felejtse el figyelembe venni a hangsávszélességre vonatkozó követelményeket is. Bár a hangadatfolyamok csak a 128 KPS-on keresztül továbbíthatók, a teljes adatfolyam gyorsan felgyorsul, ha az egyidejű adatfolyamok száma megszorozza azt.

Ez a témakör a [CDN-integráció](#enable-azure-cdn-integration)engedélyezését ismerteti. Ismerteti továbbá az előzetes beolvasást (az aktív gyorsítótárazást) és az [Origin-Assist CDN-prefektusi](#origin-assist-cdn-prefetch) koncepciót.

## <a name="considerations"></a>Megfontolandó szempontok

- A [folyamatos átviteli végpont](stream-streaming-endpoint-concept.md) `hostname` és a folyamatos átviteli URL-cím változatlan marad, függetlenül attól, hogy engedélyezi-e a CDN-t.
- Ha a tartalmat CDN használatával vagy anélkül szeretné tesztelni, hozzon létre egy másik, CDN-t nem támogató streaming-végpontot.

## <a name="enable-azure-cdn-integration"></a>Azure CDN integráció engedélyezése

> [!IMPORTANT]
> A CDN nem engedélyezhető próbaverziós vagy tanulói Azure-fiókokhoz.
>
> A CDN-integráció az összes Azure-adatközpontban engedélyezve van, kivéve a szövetségi kormányzati és a kínai régiókat.

Ha egy streaming-végpontot engedélyeztek a CDN szolgáltatással, akkor a Media Services várakozási idő van a DNS-frissítés végrehajtása előtt, hogy leképezje a folyamatos átviteli végpontot a CDN-végpontra.

Ha később le kívánja tiltani/engedélyezni szeretné a CDN-t, a folyamatos átviteli végpontnak **leállított** állapotban kell lennie. A folyamatos átviteli végpont elindítása után akár négy órát is igénybe vehet, amíg a Azure CDN-integráció engedélyezve lesz, és a módosítások az összes CDN-pop-ban aktívak lesznek. A streaming végpontot és az adatfolyamot azonban megszakítások nélkül is elindíthatja a folyamatos átviteli végpontról. Az integráció befejezését követően a stream a CDN-ből érkezik. A kiépítési időszak alatt a folyamatos átviteli végpont a **kezdeti** állapotba kerül, és megfigyelheti a teljesítmény romlását.

A standard folyamatos átviteli végpont létrehozásakor a rendszer alapértelmezés szerint a standard Verizon-t konfigurálja. A Premium Verizon vagy a standard szintű Akamai-szolgáltatók a REST API-k használatával konfigurálhatók.

A Azure Media Services integrációja Azure CDN-mel a Verizon standard streaming-végpontok **Azure CDNján** valósul meg. A prémium szintű streaming végpontok az összes **Azure CDN árképzési szint és szolgáltató** használatával konfigurálhatók.

> [!NOTE]
> A Azure CDNről a [CDN áttekintése](../../cdn/cdn-overview.md)című témakörben olvashat bővebben.

## <a name="determine-if-a-dns-change-was-made"></a>Annak megállapítása, hogy történt-e DNS-módosítás

Megadhatja, hogy a DNS-módosítás egy streaming végponton történt-e (a forgalom a Azure CDNra van irányítva) a használatával <https://www.digwebinterface.com> . Ha a azureedge.net tartományneveket lát az eredmények között, a forgalom most a CDN-re mutat.

## <a name="origin-assist-cdn-prefetch"></a>Origin-Assist CDN-Prefetch

A CDN-gyorsítótárazás egy reaktív folyamat. Ha a CDN megjósolhatja a következő objektum kérését, a CDN proaktív módon kérheti le és gyorsítótárazza a következő objektumot. Ezzel a folyamattal elérheti az objektumok összes (vagy legtöbb) gyorsítótár-találatát, ami javítja a teljesítményt.

Az előhívási elv arra törekszik, hogy az objektumokat az Internet peremén helyezi el, és azt szeretné, hogy a játékos hamarosan kéri ezeket a kéréseket, így csökkentve az adott objektumnak a lejátszóra való továbbításának idejét.

Ennek a célnak a megvalósításához a streaming-végpontnak (forrás) és a CDN-nek több módon kell dolgoznia:

- A Media Services forrásnak az "intelligencia" (forrás-assziszt) értékkel kell rendelkeznie ahhoz, hogy a CDN-t a következő objektumra tájékoztassa.
- A CDN elvégzi a kilépést és a gyorsítótárazást (CDN-prefektusi rész). A CDN-nek szüksége van az "intelligencia" kifejezésre, hogy tájékoztassa a forrást arról, hogy az a prefektus vagy a rendszeres beolvasás, kezelje a 404 válaszokat, valamint egy olyan módszert, amely elkerüli a végtelen kiindulási

### <a name="benefits"></a>Előnyök

A *forrás – támogatás CDN-kiindulási* funkció előnyei a következők:

- A prefektus javítja a videolejátszás minőségét azáltal, hogy a lejátszás során előre megtekintett video-szegmenseket, a nézők késését csökkenti a megjelenítőt, és javítja a videók szegmensének letöltési idejét. Ennek eredményeképpen gyorsabb a videó indítási ideje és az alacsonyabb újrapufferelési események.
- Ez a koncepció az általános CDN-eredetű forgatókönyvre vonatkozik, és nem korlátozódik az adathordozóra.
- A Akamai hozzáadta ezt a szolgáltatást a [Akamai Cloud embed (ACE)](https://learn.akamai.com/en-us/products/media_delivery/cloud_embed.html)szolgáltatáshoz.

> [!NOTE]
> Ez a funkció még nem alkalmazható a Media Services streaming-végponttal integrált Akamai CDN-hez. Ez azonban olyan Media Services ügyfelek számára érhető el, amelyek meglévő Akamai-szerződéssel rendelkeznek, és egyéni integrációt igényelnek a Akamai CDN és a Media Services-forrás között.

### <a name="how-it-works"></a>Működés

A `Origin-Assist CDN-Prefetch` Akamai CDN-vel közvetlen szerződéssel rendelkező ügyfelek számára a CDN támogatja a fejléceket (az élő és a video igény szerinti streaming esetében egyaránt). A szolgáltatás a következő HTTP-fejléceket foglalja magában a Akamai CDN és a Media Services-forrás között:

|HTTP-fejléc|Értékek|Küldő|Fogadó|Cél|
| ---- | ---- | ---- | ---- | ----- |
|`CDN-Origin-Assist-Prefetch-Enabled` | 1 (alapértelmezett) vagy 0 |CDN|Forrás|Annak jelzése, hogy a CDN engedélyezve van-e.|
|`CDN-Origin-Assist-Prefetch-Path`| Példa: <br/>Töredékek (videó = 1400000000, Format = mpd-Time-CMAF)|Forrás|CDN|Megadhatja a CDN elérési útját.|
|`CDN-Origin-Assist-Prefetch-Request`|1 (kirendelési kérelem) vagy 0 (normál kérelem)|CDN|Forrás|A CDN-ről érkező kérések jelzése.|

A következő lépésekkel tekintheti meg a fejléc-Exchange működés közbeni részét:

1. A Poster vagy a cURL használatával küldjön egy kérést a Media Servicesi forrásra egy hang-vagy video-szegmens vagy-töredék számára. Ügyeljen arra, hogy a kérelemben adja hozzá a fejlécet `CDN-Origin-Assist-Prefetch-Enabled: 1` .
2. A válaszban a relatív elérési úttal rendelkező fejlécnek kell megjelennie az `CDN-Origin-Assist-Prefetch-Path` értékeként.

### <a name="supported-streaming-protocols"></a>Támogatott streaming protokollok

A `Origin-Assist CDN-Prefetch` szolgáltatás a következő folyamatos átviteli protokollokat támogatja az élő és igény szerinti folyamatos átvitelhez:

* HLS v3
* HLS v4
* HLS CMAF
* KÖTŐJEL (CSF)
* KÖTŐJEL (CMAF)
* Smooth streaming

### <a name="faqs"></a>Gyakori kérdések

* Mi a teendő, ha a visszahívási útvonal URL-címe érvénytelen, hogy a CDN-kiküldés 404?

    A CDN 10 másodpercig (vagy más konfigurált érték) csak a 404-es választ gyorsítótárazza.

* Tegyük fel, hogy van egy igény szerinti videója. Ha a CDN-kivonás engedélyezve van, ez a funkció azt jelenti, hogy ha egy ügyfél az első video-szegmenst kéri, a kiküldés egy hurkot indít el, hogy az összes további videó-szegmens ugyanazzal a sávszélességgel kezdődjön?

    Nem, CDN – a prefektus csak az ügyfél által kezdeményezett kérelem/válasz után hajtható végre. CDN – a prefektust soha nem indítják el, hogy elkerülje a Meghívási ciklust.

* Mindig Origin-Assist CDN-Prefetch funkció? Hogyan lehet be-és kikapcsolni?

    Ez a szolgáltatás alapértelmezés szerint ki van kapcsolva. Az ügyfeleknek be kell kapcsolniuk a Akamai API-n keresztül.

* Élő közvetítés esetén mi történne Origin-Assist, ha a következő szegmens vagy töredék még nem érhető el?

    Ebben az esetben a Media Services a forrás nem fog megjelenni a `CDN-Origin-Assist-Prefetch-Path` fejlécben, és nem kerül sor a CDN-megadásra.

* Hogyan `Origin-Assist CDN-Prefetch` működik a dinamikus jegyzékfájlok szűrőinek használata?

    Ez a szolgáltatás a jegyzékfájltól függetlenül működik. Ha a következő részlet kívül esik egy szűrő ablakon, az URL-cím továbbra is a nyers ügyfél-jegyzékfájlba kerül, majd visszaadja a CDN-visszaküldési válasz fejlécének. Így a CDN lekéri a KÖTŐJELből/HLS/simított jegyzékfájlból kiszűrt töredékek URL-címét. A játékos azonban soha nem kap kérést a CDN-nek a töredék beolvasására, mivel ez a töredék nem szerepel a lejátszó DASH/HLS/Smooth jegyzékfájljában (a játékos nem ismeri a töredék létezését).

* A DASH MPD/HLS lejátszási lista/Smooth manifest is előhívható?

    Nem, DASH MPD, HLS Master Playlist, HLS Variant Playlist vagy Smooth manifest URL nem kerül be a prefektusi fejlécbe.

* Relatív vagy abszolút a prefektus URL-címei?

    Habár a Akamai CDN mindkét lehetőséget lehetővé teszi, a Media Services-forrás csak relatív URL-címeket biztosít a visszahívási útvonalhoz, mert nincs látható előny az abszolút URL-címek használata esetén.

* Működik ez a funkció a DRM-védelemmel ellátott tartalommal?

    Igen, mivel ez a funkció HTTP-szinten működik, nem dekódolja vagy elemzi a szegmensek/töredékek értékét. Nem érdekli, hogy a tartalom titkosítva van-e.

* Együttműködik a szolgáltatás kiszolgálóoldali ad-beszúrással (SSAI)?
    
    Ez az eredeti/fő tartalomra vonatkozik (az eredeti videó tartalma az ad beszúrása előtt), mivel a SSAI nem változtatja meg a forrás tartalmának időbélyegét a Media Services forrásból. Azt határozza meg, hogy a funkció az ad-tartalommal működik-e, attól függ, hogy az ad-forrás támogatja-e a forrás Ha például az ad-tartalmak Azure Media Services (azonos vagy különálló eredetű) is futnak, az ad-tartalmak is beolvasva lesznek.

* Működik ez a funkció a UHD/HEVC-tartalommal?

    Igen.

## <a name="ask-questions-give-feedback-get-updates"></a>Kérdések feltevése, visszajelzés küldése, frissítések beszerzése

Tekintse meg a [Azure Media Services közösségi](media-services-community.md) cikket, amely különböző módokon jelenítheti meg a kérdéseket, visszajelzéseket küldhet, és frissítéseket kaphat a Media Servicesról.

## <a name="next-steps"></a>Következő lépések

* Győződjön meg arról, hogy az [adatfolyam-végpont (forrás)](stream-streaming-endpoint-concept.md) dokumentumot tekinti át.
* Az [ebben a tárházban található](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) minta bemutatja, hogyan indíthatja el az alapértelmezett adatfolyam-végpontot a .net-tel.
