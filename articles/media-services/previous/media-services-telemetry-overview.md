---
title: Azure Media Services telemetria | Microsoft Docs
description: Ez a cikk áttekintést nyújt Microsoft Azure Media Services telemetria.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 95c20ec4-c782-4063-8042-b79f95741d28
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: b17b5901248056f6000710fa25d2ea1e9df2e2a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103009089"
---
# <a name="azure-media-services-telemetry"></a>Azure Media Services telemetria  

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Tekintse meg a legújabb, [Media Services v3](../latest/index.yml)verziót. Lásd még: [az áttelepítési útmutató v2-től v3-ig](../latest/migrate-v-2-v-3-migration-introduction.md)

A Azure Media Services (AMS) lehetővé teszi a szolgáltatásaihoz tartozó telemetria/metrikai adatok elérését. Az AMS aktuális verziója lehetővé teszi az élő **csatorna**, a **streamvégpontok** és az élő **archív** entitások telemetria-adatainak gyűjtését. 

A telemetria egy Ön által megadott Azure Storage-fiókban lévő Storage-táblába íródik (általában az AMS-fiókhoz társított Storage-fiókot kell használnia). 

A telemetria-kezelőrendszer nem kezeli az adatmegőrzést. A régi telemetria a tárolási táblák törlésével távolíthatja el.

Ez a témakör az AMS-telemetria konfigurálását és felhasználását ismerteti.

## <a name="configuring-telemetry"></a>Telemetria konfigurálása

A telemetria az összetevő szintjének részletességét is beállíthatja. Két részletességi szint létezik: "NORMAL" és "Verbose". Jelenleg mindkét szint ugyanazokat az adatokat küldi vissza. A "normál" használata javasolt. 

A következő témakörök bemutatják, hogyan engedélyezheti a telemetria:

[Telemetria engedélyezése a .NET-tel](media-services-dotnet-telemetry.md) 

[Telemetria engedélyezése a REST-tel](media-services-rest-telemetry.md)

## <a name="consuming-telemetry-information"></a>Telemetria-adatok felhasználása

A telemetria egy Azure Storage-táblába van írva abban a Storage-fiókban, amelyet akkor adott meg, amikor a Media Services-fiókhoz konfigurálta a telemetria. Ez a szakasz a metrikák tárolási tábláit ismerteti.

A telemetria-adatmennyiségeket az alábbi módokon használhatja:

- Az adatok közvetlenül az Azure Table Storageból (például a Storage SDK használatával) olvashatók be. A telemetria-tárolási táblák leírását a jelen témakör **telemetria-információk** [felhasználása](/previous-versions/azure/mt742089(v=azure.100)) című szakaszában találja.

Vagy

- [A következő témakörben](media-services-dotnet-telemetry.md) leírtak szerint használja a Media Services .net SDK támogatását a tárolási információk olvasásához. 


Az alább ismertetett telemetria séma jó teljesítményt biztosít az Azure Table Storage korlátain belül:

- Az adatok particionálása a fiókazonosító és a szolgáltatás azonosítója alapján történik, hogy az egyes szolgáltatások telemetria az egymástól függetlenül lehessen lekérdezni.
- A partíciók azt a dátumot tartalmazzák, amely a partíció méretének megfelelő felső határt adja.
- A sorfejlécek fordított időn belül lehetővé teszik a legutóbbi telemetria-elemek lekérdezését egy adott szolgáltatás esetében.

Ez lehetővé teszi, hogy a gyakori lekérdezések közül számos hatékony legyen:

- Párhuzamos, független, különálló szolgáltatásokra vonatkozó adatletöltés.
- Egy adott szolgáltatás összes adatának beolvasása dátumtartomány szerint.
- Egy szolgáltatás legfrissebb adatainak beolvasása.

### <a name="telemetry-table-storage-output-schema"></a>Telemetria-tábla tárolási kimeneti sémája

A telemetria-adatok tárolása egy "TelemetryMetrics20160321" táblában történik, ahol a "20160321" a létrehozott tábla dátuma. A telemetria rendszer minden új nap számára létrehoz egy külön táblázatot a 00:00 UTC alapján. A tábla az ismétlődő értékek tárolására szolgál, például a bitrátát az adott időkereten belül, a továbbított bájtokat stb. 

Tulajdonság|Érték|Példák/megjegyzések
---|---|---
PartitionKey|{Account ID} _ {entitás azonosítója}|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66<br/<br/>A fiók AZONOSÍTÓját a partíciós kulcs tartalmazza, hogy leegyszerűsítse azokat a munkafolyamatokat, amelyekben több Media Services fiók is ugyanarra a Storage-fiókra ír.
RowKey|{másodperc – éjfél} _ {véletlenszerű érték}|01688_00199<br/><br/>A sor kulcsa másodperc és éjfél közötti időt vesz igénybe, hogy az első n stílusú lekérdezéseket engedélyezzen a partíción belül. További információkért tekintse meg [ezt](../../cosmos-db/table-storage-design-guide.md#log-tail-pattern) a cikket. 
Időbélyeg|Dátum/idő|Automatikus időbélyeg az Azure-táblázat 2016-09-09T22:43:42.241 Z
Típus|A telemetria-adatmennyiséget biztosító entitás típusa|Channel/Streamvégpontok/Archive<br/><br/>Az esemény típusa csak egy karakterlánc-érték.
Name|A telemetria esemény neve|ChannelHeartbeat/StreamingEndpointRequestLog
ObservedTime|A telemetria esemény bekövetkezett időpontja (UTC)|2016-09-09T22:42:36.924 Z<br/><br/>A megfigyelt időt a telemetria küldő entitás (például egy csatorna) kapja meg. Az összetevők között időszinkronizálási problémák merülhetnek fel, így ez az érték körülbelül
ServiceID|{szolgáltatás azonosítója}|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
Entitás-specifikus tulajdonságok|Az esemény által meghatározottak szerint|StreamName: STREAM1, bitráta 10123,...<br/><br/>A hátralévő tulajdonságok az adott eseménytípus esetében vannak meghatározva. Az Azure Table tartalom kulcs érték párok.  (vagyis a tábla különböző sorai eltérő tulajdonságokkal rendelkeznek).

### <a name="entity-specific-schema"></a>Entitás-specifikus séma

Az entitásokra jellemző, a következő gyakorisággal leküldhető, az entitásokra vonatkozó metrikák bejegyzéseinek három típusa van:

- Folyamatos átviteli végpontok: 30 másodpercenként
- Élő csatornák: percenként
- Élő archiválás: percenként

**Streamvégpont**

Tulajdonság|Érték|Példák
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Időbélyeg|Időbélyeg|Automatikus időbélyeg az Azure Table 2016-09-09T22:43:42.241 Z
Típus|Típus|Streamvégpontok
Név|Név|StreamingEndpointRequestLog
ObservedTime|ObservedTime|2016-09-09T22:42:36.924 Z
ServiceID|Szolgáltatás azonosítója|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
HostName|A végpont állomásneve|builddemoserver.origin.mediaservices.windows.net
StatusCode|A HTTP-állapotot rögzíti|200
ResultCode|Eredmény kódja – részletek|S_OK
RequestCount|Összes kérelem az összesítésben|3
BytesSent|Eljuttatott összesített bájtok száma|2987358
ServerLatency|Kiszolgáló átlagos késése (a tárterületet is beleértve)|129
E2ELatency|Átlagos végpontok közötti késés|250

**Élő csatorna**

Tulajdonság|Érték|Példák/megjegyzések
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Időbélyeg|Időbélyeg|Automatikus időbélyeg az Azure-táblázat 2016-09-09T22:43:42.241 Z
Típus|Típus|Csatorna
Név|Név|ChannelHeartbeat
ObservedTime|ObservedTime|2016-09-09T22:42:36.924 Z
ServiceID|Szolgáltatás azonosítója|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
TrackType|Videó/hang/szöveg követésének típusa|videó/hang
TrackName|A pálya neve|videó/audio_1
Sávszélességű|Bitráta követése|785000
CustomAttributes||   
IncomingBitrate|Tényleges bejövő bitráta|784548
OverlapCount|Átfedés a betöltés során|0
DiscontinuityCount|A nyomon követés megszakítása|0
LastTimestamp|Utolsó betöltött adatidőbélyeg|1800488800
NonincreasingCount|A nem növekvő timestamp miatt elvetett töredékek száma|2
UnalignedKeyFrames|Azt jelzi, hogy a töredék (ek) (a minőségi szintek között), ahol a kulcstárolók nincsenek igazítva |Igaz
UnalignedPresentationTime|Azt jelzi, hogy a rendszer a töredék (eke) t (a minőségi szintek/sávok között) kapta-e, ahol a megjelenítési idő nincs igazítva|Igaz
UnexpectedBitrate|Igaz, ha a számított/tényleges bitráta a hang/videó nyomon követéséhez > 40 000 bps és a IncomingBitrate = = 0, illetve a IncomingBitrate és a actualBitrate eltérő a 50%-kal |Igaz
Kifogástalan|Igaz, ha <br/>overlapCount, <br/>DiscontinuityCount, <br/>NonIncreasingCount, <br/>UnalignedKeyFrames, <br/>UnalignedPresentationTime, <br/>UnexpectedBitrate<br/> mind a 0|Igaz<br/><br/>Az egészséges egy összetett függvény, amely hamis értéket ad vissza, ha a következő feltételek bármelyike fennáll:<br/><br/>-OverlapCount > 0<br/>-DiscontinuityCount > 0<br/>-NonincreasingCount > 0<br/>-UnalignedKeyFrames = = True<br/>-UnalignedPresentationTime = = True<br/>-UnexpectedBitrate = = True

**Élő Archívum**

Tulajdonság|Érték|Példák/megjegyzések
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Időbélyeg|Időbélyeg|Automatikus időbélyeg az Azure-táblázat 2016-09-09T22:43:42.241 Z
Típus|Típus|Archívum
Név|Név|ArchiveHeartbeat
ObservedTime|ObservedTime|2016-09-09T22:42:36.924 Z
ServiceID|Szolgáltatás azonosítója|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
ManifestName|Program URL-címe|Asset-eb149703-ed0a-483c-91c4-e4066e72cce3/a0a5cfbf-71ec-4BD2-8c01-a92a2b38c9ba. ISM
TrackName|A pálya neve|audio_1
TrackType|A nyomon követés típusa|Hang/videó
CustomAttribute|Hexadecimális karakterlánc, amely megkülönbözteti a különböző számok közötti különbséget azonos névvel és bitrátával (több kamera látószöge)|
Sávszélességű|Bitráta követése|785000
Kifogástalan|True (igaz), if FragmentDiscardedCount = = 0 && ArchiveAcquisitionError = = false|Igaz (ez a két érték nem szerepel a metrikában, de szerepel a forrás eseményben)<br/><br/>Az egészséges egy összetett függvény, amely hamis értéket ad vissza, ha a következő feltételek bármelyike fennáll:<br/><br/>-FragmentDiscardedCount > 0<br/>-ArchiveAcquisitionError = = True

## <a name="general-qa"></a>Általános Q&A

### <a name="how-to-consume-metrics-data"></a>Metrikai adatok felhasználása

A metrikák adatait az ügyfél Storage-fiókjában található Azure-táblák sora tárolja. Ezeket az adatmennyiségeket a következő eszközökkel lehet felhasználni:

- AMS SDK
- Microsoft Azure Storage Explorer (támogatja az exportálást vesszővel tagolt érték formátumba, és az Excelben dolgozza fel azokat)
- REST API

### <a name="how-to-find-average-bandwidth-consumption"></a>A sávszélesség átlagos felhasználásának megállapítása

Az átlagos sávszélesség-használat a BytesSent átlaga egy adott időtartam alatt.

### <a name="how-to-define-streaming-unit-count"></a>Hogyan határozható meg a folyamatos átviteli egységek száma?

A folyamatos átviteli egységek száma meghatározható a szolgáltatás folyamatos átviteli végpontjának maximális átviteli sebessége alapján, osztva egy streaming végpont maximális átviteli sebességével. Egy folyamatos átviteli végpont maximális felhasználható átviteli sebessége 160 Mbps.
Tegyük fel például, hogy az ügyfél szolgáltatásának maximális átviteli sebessége 40 MBps (a BytesSent maximális értéke egy adott időtartamon belül). Ezután a folyamatos átviteli egységek száma egyenlő (40 MBps) * (8 bit/bájt)/(160 Mbps) = 2 folyamatos átviteli egység.

### <a name="how-to-find-average-requestssecond"></a>Az átlagos kérelmek/másodpercek megkeresése

A kérelmek másodpercenkénti átlagos számának megkereséséhez a kérelmek (RequestCount) átlagos számának kiszámítása egy adott időtartamon keresztül.

### <a name="how-to-define-channel-health"></a>A csatorna állapotának meghatározása

A csatorna állapota összetett logikai függvényként határozható meg, amely akkor hamis, ha a következő feltételek bármelyike fennáll:

- OverlapCount > 0
- DiscontinuityCount > 0
- NonincreasingCount > 0
- UnalignedKeyFrames = = True 
- UnalignedPresentationTime = = True 
- UnexpectedBitrate = = True


### <a name="how-to-detect-discontinuities"></a>Hogyan lehet felderíteni a folytonosságot?

A megszakítások észleléséhez keresse meg az összes olyan csatorna-adatbejegyzést, ahol a DiscontinuityCount > 0. A megfelelő ObservedTime timestamp azt jelzi, hogy hányszor történtek a megszakítások.

### <a name="how-to-detect-timestamp-overlaps"></a>Az időbélyeg átfedésének észlelése

Az időbélyeg átfedésének észleléséhez keresse meg az összes olyan csatorna-adatbejegyzést, ahol az OverlapCount > 0. A megfelelő ObservedTime timestamp az időbélyeg átfedésének időpontját jelzi.

### <a name="how-to-find-streaming-request-failures-and-reasons"></a>Hogyan találhatják meg a folyamatos átviteli kérelmekkel kapcsolatos hibákat és okokat?

Az adatfolyam-kérelmekkel kapcsolatos hibák és okok megtalálásához keresse meg az összes streaming Endpoint adatbejegyzést, ahol a ResultCode nem egyenlő S_OK. A kérelem meghibásodásának okát a megfelelő StatusCode mező jelzi.

### <a name="how-to-consume-data-with-external-tools"></a>Hogyan kell az adatfelhasználást külső eszközökkel használni?

A metrikai adatok feldolgozhatók és megjeleníthetők a következő eszközök használatával:

- PowerBI
- Application Insights
- Azure Monitor (korábban Shoebox)
- AMS élő irányítópult
- Azure Portal (kiadás függőben)

### <a name="how-to-manage-data-retention"></a>Az adatmegőrzés kezelése

A telemetria rendszere nem biztosítja az adatmegőrzési felügyeletet, vagy a régi rekordok automatikus törlését. Így a régi rekordokat manuálisan kell kezelni és törölni a Storage-táblából. A Storage SDK-val megtekintheti a módját.

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
