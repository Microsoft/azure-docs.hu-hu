---
title: élő események és élő kimenetek fogalmak a Azure Media Services v3-ben: Azure Media Services Leírás: Ez a témakör áttekintést nyújt az élő eseményekről és a Azure Media Services v3 élő kimenetéről.
szolgáltatások: Media-Services documentationcenter: ' ' Author: IngridAtMicrosoft Manager: femila Editor: ' '

MS. Service: Media-Services MS. munkaterhelés: Media ms.tgt_pltfrm: na MS. devlang: ne MS. topic: konceptuális MS. Date: 10/23/2020 MS. Author: inhenkel

---
# <a name="live-events-and-live-outputs-in-media-services"></a>Élő események és élő kimenetek Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services lehetővé teszi, hogy élő eseményeket nyújtson az ügyfeleknek az Azure-felhőben. Az élő adatfolyam-továbbítási események Media Services v3-ban való beállításához ismernie kell a cikkben tárgyalt fogalmakat.

> [!TIP]
> Media Services v2 API-kból áttelepítést igénylő ügyfelek esetében az **élő esemény** entitás a v2-es **csatornát** váltja fel, és az **élő kimenet** lecseréli a **programot**.

## <a name="live-events"></a>Élő események

Az élő [események](/rest/api/media/liveevents) az élő video-hírcsatornák betöltéséhez és feldolgozásához felelősek. Élő esemény létrehozásakor létrejön egy elsődleges és egy másodlagos bemeneti végpont, amelynek használatával élő jeleket küldhet egy távoli kódolóból. A távoli élő kódoló az [RTMP](https://www.adobe.com/devnet/rtmp.html) vagy a [Smooth streaming](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251) (darabolt MP4) bemeneti protokoll használatával küldi el a hozzájárulási csatornát az adott bemeneti végpontnak. Az RTMP betöltési protokoll esetében a tartalom a (z) Clear ( `rtmp://` ) vagy a Wire () biztonságos titkosításával is elvégezhető `rtmps://` . A Smooth Streaming betöltési protokoll esetében a támogatott URL-sémák a `http://` vagy a `https://` .  

## <a name="live-event-types"></a>Élő események típusai

Egy [élő esemény](/rest/api/media/liveevents) lehet egy *átmenő* (egy helyszíni élő kódoló több bitrátás streamet küld) vagy *élő kódolást* (a helyszíni élő kódoló egyetlen sávszélességű adatfolyamot küld). A típusok beállítása a [LiveEventEncodingType](/rest/api/media/liveevents/create#liveeventencodingtype)használatával történik a létrehozás során:

* **LiveEventEncodingType. None**: a helyszíni élő kódoló több bitrátás streamet küld. A betöltött adatfolyam további feldolgozás nélkül halad át az élő eseményen. Más néven áteresztő üzemmód.
* **LiveEventEncodingType. Standard**: a helyszíni élő kódoló egyetlen sávszélességű adatfolyamot küld az élő eseménynek, és Media Services több bitrátás adatfolyamot hoz létre. Ha a hozzájárulási hírcsatorna 720p vagy magasabb felbontású, a **Default720p** -készlet 6 feloldási/bitrátás párokat fog kódolni.
* **LiveEventEncodingType. Premium1080p**: a helyszíni élő kódoló egyetlen sávszélességű adatfolyamot küld az élő eseménynek, és Media Services több bitrátás adatfolyamot hoz létre. A Default1080p-készlet meghatározza a feloldási/bitráta párok kimeneti készletét.

### <a name="pass-through"></a>Továbbítás

![áteresztő élő esemény Media Services példa diagrammal](./media/live-streaming/pass-through.svg)

A továbbítás **élő eseményének** használatakor a helyszíni élő kódoló több bitráta-videó stream létrehozásához és az élő eseményhez való hozzájáruláshoz (RTMP vagy darabolt MP4 protokoll használatával) küldi el. Az élő esemény ezután a bejövő videó streameken keresztül végez további feldolgozás nélkül. Egy ilyen átmenő élő esemény a hosszan futó élő eseményekre vagy 24x365 lineáris élő közvetítésre van optimalizálva. Az ilyen típusú élő események létrehozásakor adja meg a none értéket (LiveEventEncodingType. None).

A bemeneti adatokat legfeljebb 4K felbontással és 60 képkocka/másodperc képkockasebességgel továbbíthatja, H.264/AVC vagy H.265/HEVC videokodekkel és AAC (AAC-LC, HE-AACv1 vagy HE-AACv2) hangkodekkel. További információ: [élő események típusai összehasonlítás](live-event-types-comparison.md).

> [!NOTE]
> A továbbítási módszer használata a leggazdaságosabb módja az élő közvetítésnek, ha hosszú időn keresztül végez több eseményt, és már befektetett a helyszíni kódolóba. Tekintse meg a [díjszabás](https://azure.microsoft.com/pricing/details/media-services/) részleteit.
>

Tekintse meg a .NET-kód példáját a [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126)-ben.

### <a name="live-encoding"></a>Live Encoding  

![élő kódolás Media Services példa diagrammal](./media/live-streaming/live-encoding.svg)

Ha a Media Services élő kódolást használ, a helyszíni élő kódolót úgy konfigurálja, hogy egyetlen bitráta-videót küldjön az élő eseménynek (RTMP vagy Fragmented-Mp4 protokollt használó) hozzájárulási csatornának. Ezután beállíthat egy élő eseményt úgy, hogy az a bejövő egyszeri sávszélességű adatfolyamot [több bitrátás videó streambe](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)kódolja, és elérhetővé teszi a kimenetet a továbbításhoz az eszközökön, például az MPEG-Dash, a HLS és a Smooth streaming protokollok használatával.

Ha élő kódolást használ, a hozzájárulási csatornát csak a 30 képkocka/másodperces képkocka sebességű felbontásban, a H. 264/AVC Video Codec és az AAC (AAC-LC, it-AACv1 vagy AACv2) hangkodek használatával küldheti el. Vegye figyelembe, hogy az áteresztő élő események a 60-es képkockán/másodpercen belüli 4K-os felbontást támogatják. További információ: [élő események típusai összehasonlítás](live-event-types-comparison.md).

Az élő kódoló kimenetében található felbontásokat és bitrátákat az előre beállított érték határozza meg. Ha **standard** Live Encodert (LiveEventEncodingType. Standard) használ, akkor a *Default720p* előre definiált hat felbontási/átviteli sebesség párokat határoz meg, amely a 720P-on 3,5 Mbps-ig, a 192p-on 200 kbps. Ellenkező esetben, ha **Premium1080p** élő kódolót (LiveEventEncodingType. Premium1080p) használ, akkor a *Default1080p* -készlet hat felbontási/bitsűrűségi párokat határoz meg, amely az 1080p 3,5 Mbps-ról 200 kbps-ra van állítva. További információkat a [rendszerbeállítás-készletekkel](live-event-types-comparison.md#system-presets) kapcsolatos szakaszban talál.

> [!NOTE]
> Ha testre kell szabnia az élő kódolási beállításkészletet, nyisson meg egy támogatási jegyet Azure Portalon keresztül. Határozza meg a kívánt felbontást és bitrátát. Győződjön meg arról, hogy a 720p-ban csak egy réteg található (ha a standard Live Encoder esetében egy beállításkészletet igényel) vagy 1080p (ha egy Premium1080p élő kódolóhoz van beállítva), és legfeljebb 6 réteget.

## <a name="creating-live-events"></a>Élő események létrehozása

### <a name="options"></a>Beállítások

Élő események létrehozásakor a következő beállításokat adhatja meg:

* Az élő esemény nevét és leírását is megadhatja.
* A Felhőbeli kódolás átmenő (nincs felhőalapú kódolás), Standard (legfeljebb 720p) vagy prémium (legfeljebb 1080p) lehet. A standard és a prémium szintű kódoláshoz kiválaszthatja a kódolt videó nyújtási módját.
  * Nincs: szigorúan tiszteletben tartja a kódolási beállításkészletben megadott kimeneti felbontást, figyelembe véve a képpontok oldalarányát vagy a bemeneti videó megjelenítési méretarányát.
  * AutoSize: felülbírálja a kimeneti felbontást, és úgy módosítja, hogy megfeleljen a bemenet megjelenítési méretarányának, kitöltés nélkül. Ha például a bemenet 1920 × 1080, és a kódolási beállításkészlet 1280x1280 kér, akkor a rendszer felülbírálja az előre beállított értéket, a kimenet pedig a 1280x720 címen, amely fenntartja a 16:9-es bemeneti méretarányt.
  * Automatikus méretezés: a kimenetet (a levélszekrény vagy az oszlop mezővel együtt) kitölti a kimeneti felbontás tiszteletben tartásával, miközben azt is biztosítja, hogy a kimenetben lévő aktív videó régiója ugyanolyan méretarányú legyen, mint a bemenet. Ha például a bemenet 1920 × 1080, és a kódolási beállításkészlet kéri a 1280x1280, akkor a kimenet a következő helyen található: 1280x1280, amely a 1280x720 belső téglalapját tartalmazza a méretarányban, a 16:9, a bal és a jobb oldalon pedig az oszlopos terület 280 képpont szélességét.
* Streaming Protocol (jelenleg az RTMP és a Smooth Streaming protokollok támogatottak). A protokoll beállítás nem módosítható, amíg az élő esemény vagy a hozzá tartozó élő kimenet fut. Ha eltérő protokollokra van szüksége, hozzon létre egy külön élő eseményt az egyes streaming protokollokhoz.
* A bemeneti azonosító, amely az élő esemény bemeneti adatfolyamának globálisan egyedi azonosítója.
* Statikus állomásnév előtagja, amely tartalmazza a none értéket (ebben az esetben a rendszer véletlenszerű 128 bit hexadecimális karakterláncot használ), használja az élő esemény nevét, vagy használja az egyéni nevet.  Ha az ügyfél nevének használatát választja, ez az érték az egyéni állomásnév-előtag.
* Az élő közvetítés és a lejátszás közötti teljes késés csökkentése érdekében állítsa be a bemeneti kulcs keretének intervallumát (másodpercben), amely a HLS-kimenetben lévő egyes adathordozók szegmensének időtartama (másodpercben). Az értéknek nullától eltérő egész számnak kell lennie a 0,5 és 20 másodperc között.  Az érték alapértelmezett értéke 2 másodperc, ha a bemeneti vagy a kimeneti kulcs keretének egyik intervalluma *sincs* megadva. A kulcs keretének intervalluma csak átmenő eseményeken engedélyezett.
* Az esemény létrehozásakor beállíthatja az automatikus indítást. Ha az autostart értéke TRUE (igaz), a rendszer az élő eseményt a létrehozás után indítja el. A számlázás azonnal elindul, amint az élő esemény elindul. A további számlázás leállításához explicit módon hívnia kell az élő esemény erőforrásának leállítását. Azt is megteheti, hogy elindíthatja az eseményt, amikor készen áll a folyamatos átvitelre.

> [!NOTE]
> A maximális frameráta 30 fps a standard és a Premium kódoláshoz.

## <a name="standby-mode"></a>Készenléti mód

Élő esemény létrehozásakor beállíthatja készenléti módba. Amíg az esemény készenléti állapotban van, szerkesztheti a leírást, a statikus állomásnév előtagját, és korlátozhatja a bemeneti és az előnézeti hozzáférési beállításokat.  A készenléti mód továbbra is számlázható mód, de a díjszabása eltérő, mint amikor egy élő streamet indít el.

További információ: [élő események állapota és számlázása](live-event-states-billing.md).

* IP-korlátozások a betöltési és az előnézeti címen. Megadhatja azokat az IP-címeket, amelyek számára engedélyezett az élő esemény Videójának beolvasása. Az engedélyezett IP-címek köre tartalmazhat egyetlen IP-címet (például „10.0.0.1”), vagy egy IP-tartományt, amelyet egy IP-cím és egy CIDR alhálózati maszk (például„10.0.0.1/22”) vagy egy IP-cím és egy pontozott decimális alhálózati maszk (például „10.0.0.1(255.255.252.0)”) segítségével lehet megadni.
<br/><br/>
Ha nincs megadva IP-cím, és nincs szabály definíciója, akkor a rendszer nem engedélyezi az IP-címet. Ha az összes IP-címnek szeretne engedélyt adni, hozzon létre egy szabályt, és állítsa be a következő értéket: 0.0.0.0/0.<br/>Az IP-címnek a következő formátumok egyikében kell lennie: IpV4-cím, amely négy számot vagy CIDR-címtartományt tartalmaz.
<br/><br/>
Ha bizonyos IP-címeket szeretne engedélyezni a saját tűzfalakon, vagy szeretné korlátozni az élő eseményekre vonatkozó adatokat az Azure-beli IP-címekre, töltsön le egy JSON-fájlt az [Azure Datacenter IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653)közül. A fájllal kapcsolatos részletekért válassza a lap **részletek** szakaszát.

* Az esemény létrehozásakor dönthet úgy, hogy bekapcsolja az élő átírásokat. Alapértelmezés szerint az élő átírás le van tiltva. További információ az élő átírásról: [élő átírás](live-transcription.md).

### <a name="naming-rules"></a>Elnevezési szabályok

* Az élő esemény maximális neve 32 karakter.
* A névnek ezt a [regex](/dotnet/standard/base-types/regular-expression-language-quick-reference) -mintát kell követnie: `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$` .

Lásd még: [adatfolyam-végpontok elnevezési konvenciói](streaming-endpoint-concept.md#naming-convention).

> [!TIP]
> Az élő esemény nevének egyediségének biztosításához létrehozhat egy GUID azonosítót, és eltávolíthatja az összes kötőjelet és kapcsos zárójelet (ha van ilyen). A karakterlánc az összes élő eseménynél egyedi lesz, és a hossza garantált, hogy 32.

## <a name="live-event-ingest-urls"></a>Élő esemény betöltésének URL-címei

Az élő esemény létrejötte után betöltheti az élő helyszíni kódolóhoz megadható betöltési URL-címeket. Az élő kódoló ezekre az URL-címekre küldi a bemeneti élő streamet. További információ: [ajánlott helyszíni élő kódolók](recommended-on-premises-live-encoders.md).

>[!NOTE]
> A 2020-05-01 API-kiadástól kezdve a hiúság URL-címeinek neve statikus állomásnév

Kreatív vagy nem kreatív URL-címeket is használhat.

> [!NOTE]
> Ahhoz, hogy a betöltési URL-cím prediktív, állítsa a "Vanity" módot.

* Nem hiúság URL-címe

    A nem hiúságos URL-cím az alapértelmezett mód a Media Services v3-ban. Előfordulhat, hogy az élő esemény gyors, de a betöltési URL-cím csak az élő esemény indításakor ismert. Az URL-cím akkor változik meg, ha leállítja/elindítja az élő eseményt. A nem hiúság olyan helyzetekben hasznos, amikor a végfelhasználó egy olyan alkalmazással szeretne adatfolyamot használni, ahol az alkalmazás egy élő eseményt szeretne kapni, és a dinamikus betöltési URL-cím nem jelent problémát.

    Ha egy ügyfélalkalmazás nem kell előkészítenie a betöltési URL-címet az élő esemény létrehozása előtt, Media Services automatikusan hozza létre az élő esemény hozzáférési jogkivonatát.

* A hiúság URL-címe

    A hiúság üzemmódot a hardveres szórásos kódolókat használó nagyméretű média-műsorszolgáltatók használják, és nem szeretnék újrakonfigurálni a kódolókat az élő esemény indításakor. Ezek a műsorszolgáltatók olyan prediktív betöltési URL-címet szeretnének használni, amely idővel nem változik.

    > [!NOTE]
    > A Azure Portal a Vanity URL-cím neve "*statikus állomásnév előtagja*".

    Ha ezt a módot az API-ban szeretné megadni, állítsa a következőre `useStaticHostName` `true` : létrehozás időpontja (alapértelmezett érték `false` ). Ha a `useStaticHostname` értéke TRUE (igaz), a `hostnamePrefix` Megadja az állomásnév első részét az élő esemény előnézetéhez, és betölti a végpontokat. Az utolsó állomásnév ezen előtag kombinációja lenne, a Media Service-fiók neve és egy rövid kód a Azure Media Services adatközponthoz.

    Ha el szeretné kerülni az URL-cím véletlenszerű tokenjét, a létrehozáskor is át kell adnia a saját hozzáférési tokenjét ( `LiveEventInput.accessToken` ).  A hozzáférési jogkivonatnak érvényes GUID-karakterláncnak kell lennie (kötőjelekkel vagy anélkül). A mód beállítása után nem frissíthető.

    A hozzáférési tokennek egyedinek kell lennie az adatközpontban. Ha az alkalmazásnak a Vanity URL-címet kell használnia, akkor azt javasoljuk, hogy mindig hozzon létre egy új GUID-példányt a hozzáférési jogkivonathoz (a meglévő GUID-azonosítók újbóli használata helyett).

    A következő API-k használatával engedélyezheti a hiúság URL-címét, és a hozzáférési tokent érvényes GUID azonosítóra (például:) állíthatja be `"accessToken": "1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"` .  

    |Nyelv|A hiúság URL-címének engedélyezése|Hozzáférési jogkivonat beállítása|
    |---|---|---|
    |REST|[Properties. vanityUrl](/rest/api/media/liveevents/create#liveevent)|[LiveEventInput. accessToken](/rest/api/media/liveevents/create#liveeventinput)|
    |parancssori felület|[--Vanity-URL](/cli/azure/ams/live-event?view=azure-cli-latest#az-ams-live-event-create)|[--Access-Token](/cli/azure/ams/live-event?view=azure-cli-latest#optional-parameters)|
    |.NET|[LiveEvent.VanityUrl](/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEvent_VanityUrl)|[LiveEventInput. AccessToken](/dotnet/api/microsoft.azure.management.media.models.liveeventinput.accesstoken?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEventInput_AccessToken)|

### <a name="live-ingest-url-naming-rules"></a>Élő bemeneti URL-címek elnevezési szabályai

* Az alábbi *véletlenszerű* sztring egy 128 bites hexadecimális szám (amely 32 karakterből áll 0-9-ig és a-f-ig).
* *hozzáférési jogkivonat*: a Vanity mód használatakor beállított érvényes GUID-karakterlánc. Például: `"1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`.
* *stream neve*: megadja az adott kapcsolatok adatfolyamának nevét. Az adatfolyam-név értékét általában a használt élő kódoló adja hozzá. Az élő kódoló úgy is beállítható, hogy bármilyen nevet használjon a kapcsolódás leírásához, például: "video1_audio1", "video2_audio1", "Stream".

#### <a name="non-vanity-url"></a>Nem hiúság URL-címe

##### <a name="rtmp"></a>RTMP

`rtmp://<random 128bit hex string>.channel.media.azure.net:1935/live/<auto-generated access token>/<stream name>`<br/>
`rtmp://<random 128bit hex string>.channel.media.azure.net:1936/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2935/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2936/live/<auto-generated access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Smooth streaming

`http://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>

#### <a name="vanity-url"></a>A hiúság URL-címe

A következő elérési utakban az `<live-event-name>` eseménynek vagy az élő esemény létrehozásához használt egyéni névnek a nevét jelenti.

##### <a name="rtmp"></a>RTMP

`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1935/live/<your access token>/<stream name>`<br/>
`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1936/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2935/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2936/live/<your access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Smooth streaming

`http://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>

## <a name="live-event-preview-url"></a>Élő esemény előnézetének URL-címe

Miután az élő esemény megkezdi a hozzájárulási hírcsatorna fogadását, az előzetes verziójú végponton megtekintheti és ellenőrizheti, hogy a további közzététel előtt megkapja-e az élő streamet. Miután ellenőrizte, hogy az előnézeti adatfolyam megfelelő-e, a Live Event használatával elérhetővé teheti az élő streamet egy vagy több (előre létrehozott) streaming végponton keresztül. Ennek elvégzéséhez hozzon létre egy új [élő kimenetet](/rest/api/media/liveoutputs) az élő eseményen.

> [!IMPORTANT]
> A folytatás előtt győződjön meg arról, hogy a videó az előzetes verzió URL-címére áramlik.

## <a name="live-event-long-running-operations"></a>Élő esemény hosszan futó műveletei

Részletekért lásd: [hosszan futó műveletek](media-services-apis-overview.md#long-running-operations).

## <a name="live-outputs"></a>Élő kimenetek

Ha a stream az élő eseménybe áramlik, megkezdheti a folyamatos átviteli eseményt egy [eszköz](/rest/api/media/assets), egy [élő kimenet](/rest/api/media/liveoutputs)és a [folyamatos átviteli lokátor](/rest/api/media/streaminglocators)létrehozásával. az élő kimenet archiválja a streamet, és elérhetővé teszi a nézők számára a [folyamatos átviteli végponton](/rest/api/media/streamingendpoints)keresztül.  

Az élő kimenetekkel kapcsolatos részletes információkért lásd: [FELHŐALAPÚ DVR használata](live-event-cloud-dvr.md).

## <a name="frequently-asked-questions"></a>Gyakori kérdések

Tekintse meg a [Gyakori kérdések](frequently-asked-questions.md#live-streaming) című cikket.

## <a name="ask-questions-and-get-updates"></a>Kérdések és frissítések kérése

Tekintse meg a [Azure Media Services közösségi](media-services-community.md) cikket, amely különböző módokon jelenítheti meg a kérdéseket, visszajelzéseket küldhet, és frissítéseket kaphat a Media Servicesról.

## <a name="next-steps"></a>Következő lépések

[Élő közvetítés – oktatóanyag](stream-live-tutorial-with-api.md)
