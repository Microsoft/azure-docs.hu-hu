---
title: Az élő közvetítés áttekintése
description: Ez a cikk áttekintést nyújt az élő közvetítésről Azure Media Services v3 használatával.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: f480f7821ea3443c76c551f45ac74d136f417060
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277300"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Élő közvetítés a Azure Media Services v3-val

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

A Azure Media Services lehetővé teszi, hogy élő eseményeket nyújtson az ügyfeleknek az Azure-felhőben. Az élő események Media Servicessal való továbbításához a következőkre lesz szüksége:  

- Az élő esemény rögzítésére szolgáló kamera.<br/>A beállítási ötletekért tekintse meg az [egyszerű és a hordozható eseményekre vonatkozó videós eszközöket]( https://link.medium.com/KNTtiN6IeT).

    Ha nem rendelkezik hozzáféréssel egy kamerához, az eszközök (például a [Wirecast](https://www.telestream.net/wirecast/overview.htm) ) segítségével élő hírcsatornát hozhatnak elő a videofájl használatával.
- Egy élő videó kódoló, amely egy kamerából (vagy egy másik eszközről, például egy laptopból) származó jeleket alakít át egy Media Servicesba küldendő közreműködői adatcsatornába. A hozzájárulási hírcsatorna tartalmazhat a hirdetésekkel kapcsolatos jeleket, például a SCTE-35 jelölőket.<br/>A javasolt élő adatfolyam-kódolók listáját lásd: [élő adatfolyam-kódolók](encode-recommended-on-premises-live-encoders.md). Továbbá tekintse meg ezt a blogot: [élő streaming Production with OBS](https://link.medium.com/ttuwHpaJeT).
- A Media Services összetevői, amelyek lehetővé teszik az élő események betöltését, beolvasását, becsomagolását, rögzítését, titkosítását és továbbítását az ügyfelek számára, illetve a CDN-t további terjesztés céljából.

Azon ügyfelek számára, akik nagy internetes célközönségeknek szánt tartalmat szeretnének kézbesíteni, javasoljuk, hogy engedélyezze a CDN használatát a [streaming végponton](stream-streaming-endpoint-concept.md).

Ez a cikk áttekintést nyújt az élő közvetítésről Media Services és más kapcsolódó cikkekre mutató hivatkozásokat tartalmaz.
 
> [!NOTE]
> A [Azure Portal](https://portal.azure.com/) a v3 [élő események](live-event-outputs-concept.md)kezelésére, a v3- [eszközök](assets-concept.md)megtekintésére, az API-k elérésére vonatkozó információk beszerzésére használható. Az összes többi felügyeleti feladathoz (például átalakításokhoz és feladatokhoz) használja a [REST API](/rest/api/media/), a [CLI](/cli/azure/ams)vagy az egyik támogatott [SDK](media-services-apis-overview.md#sdks)-t.

## <a name="dynamic-packaging-and-delivery"></a>Dinamikus csomagolás és kézbesítés

A Media Services segítségével kihasználhatja a [dinamikus csomagolás](encode-dynamic-packaging-concept.md)előnyeit, így az élő streameket az MPEG Dash-ben [, a HLS-ban Smooth streaming és](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) a szolgáltatásba küldött közreműködői adatcsatornán keresztül is megtekintheti és közvetítheti. A megtekintők az élő streamet bármilyen HLS, KÖTŐJELtel vagy Smooth Streaming kompatibilis játékossal játszhatják fel. A webes vagy mobil alkalmazásaiban [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) használhatja az adatfolyam továbbítására ezen protokollok bármelyikében.

## <a name="dynamic-encryption"></a>Dinamikus titkosítás

A dinamikus titkosítás lehetővé teszi, hogy az élő vagy igény szerinti tartalmakat az AES-128 vagy a három jelentős digitális jogkezelési (DRM) rendszerű rendszeren keresztül dinamikusan titkosítsa: Microsoft PlayReady, Google Widevine és Apple FairPlay. A Media Services egy szolgáltatást is biztosít az AES-kulcsok és a DRM (PlayReady, Widevine és FairPlay) licencek továbbítására a hitelesítő ügyfelek számára. További információ: [dinamikus titkosítás](drm-content-protection-concept.md).

> [!NOTE]
> A Widevine a Google Inc által biztosított szolgáltatás, és a Google, Inc. szolgáltatási és adatvédelmi szabályzatának feltételei vonatkoznak rá.

## <a name="dynamic-filtering"></a>Dinamikus szűrés

A dinamikus szűréssel szabályozható a játékosok számára eljuttatott zeneszámok, formátumok, bitráták és a megjelenítési idő Windows-száma. További információ: [szűrők és dinamikus jegyzékfájlok](filters-dynamic-manifest-concept.md).

## <a name="live-event-types"></a>Élő események típusai

Az élő [események](/rest/api/media/liveevents) az élő video-hírcsatornák betöltéséhez és feldolgozásához felelősek. Egy élő esemény lehet egy *átmenő* (egy helyszíni élő kódoló több bitrátás streamet küld) vagy *élő kódolást* (a helyszíni élő kódoló egyetlen sávszélességű adatfolyamot küld). A Media Services v3 élő közvetítésével kapcsolatos részletekért lásd: [élő események és élő kimenetek](live-event-outputs-concept.md).

### <a name="pass-through"></a>Továbbítás

![Azt bemutató ábra, hogy a rendszer beolvassa és dolgozza fel a továbbított élő esemény videóit és hangcsatornáit.](./media/live-streaming/pass-through.svg)

A továbbítás **élő eseményének** használatakor a helyszíni élő kódoló több bitráta-videó stream létrehozásához és az élő eseményhez való hozzájáruláshoz (RTMP vagy darabolt MP4 bemeneti protokoll használatával) küldje el. Az élő esemény ezután a bejövő videó streameket a dinamikus csomagolóba (streaming Endpoint) továbbítja a további átkódolás nélkül. Egy ilyen átmenő élő esemény a hosszan futó élő eseményekre vagy 24x365 lineáris élő közvetítésre van optimalizálva. 

### <a name="live-encoding"></a>Live Encoding  

![live encoding](./media/live-streaming/live-encoding.svg)

Ha Media Services használatával Felhőbeli kódolást használ, a helyszíni élő kódolót úgy kell konfigurálnia, hogy egyetlen bitráta-videót küldjön az élő eseményhez (az RTMP vagy a darabolt MP4 bemeneti protokoll használatával). Az élő esemény átkódolja a bejövő egyszeri sávszélességű adatfolyamot [több bitrátás videó streamre](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) , különböző felbontások mellett, és így elérhetővé teszi a lejátszási eszközök számára az iparági szabványnak megfelelő protokollok, például az MPEG-Dash, az Apple http Live Streaming (HLS) és a Microsoft Smooth streaming használatával. 

### <a name="live-transcription-preview"></a>Élő átírás (előzetes verzió)

Az élő átírás egy olyan szolgáltatás, amellyel áteresztő vagy élő kódolású élő eseményeket lehet használni. További információ: [élő átírás](live-event-live-transcription-how-to.md). Ha ez a szolgáltatás engedélyezve van, a szolgáltatás a Cognitive Services [beszéd-szöveg](../../cognitive-services/speech-service/speech-to-text.md) funkciója segítségével átmásolja a beérkező szövegben szereplő szóbeli szavakat. Ezt a szöveget ezután elérhetővé kell tenni a videóval és hanggal együtt az MPEG-DASH és a HLS protokollok esetében.

> [!NOTE]
> Az élő átírás jelenleg az USA 2. nyugati régiójában érhető el előzetes verziójú szolgáltatásként.

## <a name="live-streaming-workflow"></a>Élő adatfolyam-továbbítási munkafolyamat

Az Media Services v3 élő közvetítési munkafolyamatának megismeréséhez először tekintse át és Ismerje meg a következő fogalmakat: 

- [Streamvégpontok](stream-streaming-endpoint-concept.md)
- [Élő események és élő kimenetek](live-event-outputs-concept.md)
- [Streameléskeresők](stream-streaming-locators-concept.md)

### <a name="general-steps"></a>Általános lépések

1. Győződjön meg arról, hogy a Media Services-fiókban fut a **folyamatos átviteli végpont** (forrás). 
2. Hozzon létre egy [élő eseményt](live-event-outputs-concept.md). <br/>Az esemény létrehozásakor megadhatja az automatikus indítást. Azt is megteheti, hogy elindíthatja az eseményt, amikor készen áll a folyamatos átvitelre.<br/> Ha az Automatikus indítás értéke TRUE (igaz), akkor az élő esemény a létrehozás után rögtön elindul. A számlázás azonnal elindul, amint az élő esemény elindul. A további számlázás leállításához explicit módon hívnia kell az élő esemény erőforrásának leállítását. További információ: [élő események állapota és számlázása](live-event-states-billing-concept.md).
3. Töltse le a betöltési URL-címeket, és konfigurálja a helyszíni kódolót úgy, hogy az URL-cím használatával küldje el a hozzájárulási csatornát.<br/>Lásd: [ajánlott élő kódolók](encode-recommended-on-premises-live-encoders.md).
4. Szerezze be az előnézeti URL-címet, és annak ellenőrzéséhez, hogy a kódolóból érkező adatok fogadása ténylegesen megtörténik-e.
5. Hozzon létre **egy új objektum** objektumot. 

    Minden élő kimenet egy adategységhez van társítva, amelyet a videó a társított Azure Blob Storage-tárolóba való rögzítéséhez használ. 
6. Hozzon létre egy **élő kimenetet** , és használja a létrehozott eszköz nevét, hogy az adatfolyam archiválható legyen az objektumba.

    Az élő kimenetek a létrehozás után kezdődnek, és a törléskor leállnak. Ha törli az élő kimenetet, nem törli az objektum mögöttes eszközét és tartalmát.
7. Hozzon létre egy **streaming-keresőt** a [beépített folyamatos átviteli házirend-típusokkal](stream-streaming-policy-concept.md).

    Az élő kimenet közzétételéhez létre kell hoznia egy folyamatos átviteli lokátort a társított objektumhoz. 
8. A **streaming-lokátor** elérési útjának listázása a használni kívánt URL-címek visszaszerzéséhez (ezek a determinisztikus).
9. Szerezze be annak a **streaming-végpontnak** (forrásnak) az állomásnevét, amelyről a streamet szeretné továbbítani.
10. A teljes URL-cím lekéréséhez kombinálja a 8. lépésből származó URL-címet a 9. lépésben szereplő állomásnévvel.
11. Ha szeretné megszüntetni az **élő esemény** megkeresését, le kell állítania az eseményt, és törölnie kell a **folyamatos átviteli lokátort**.
12. Ha befejezte az esemény streamelését, és törölni szeretné a korábban kiosztott erőforrásokat, kövesse az alábbi eljárást.

    * Állítsa le a stream továbbítását a kódolóban.
    * Állítsa le az élő eseményt. Az élő esemény leállítását követően nem számítunk fel díjat. A betöltési URL-cím nem módosul, ezért a csatorna ismételt elindításához nem szükséges újrakonfigurálni a kódolót.
    * Leállíthatja a folyamatos átviteli végpontot, hacsak nem szeretné, hogy az élő esemény archívumát igény szerinti adatfolyamként adja meg. Ha az élő esemény leállított állapotban van, nem számítunk fel díjat.

Az az objektum, amelyet az élő kimenet archivál, automatikusan egy igény szerinti eszközvé válik az élő kimenet törlésekor. Az élő események leállításához minden élő kimenetet törölnie kell. A nem kötelező jelző [removeOutputsOnStop](/rest/api/media/liveevents/stop#request-body) használatával automatikusan eltávolíthatja az élő kimeneteket a leállítás után. 

> [!TIP]
> Lásd: [élő közvetítés oktatóanyaga](stream-live-tutorial-with-api.md), a cikk megvizsgálja a fent ismertetett lépéseket megvalósító kódot.

## <a name="other-important-articles"></a>Egyéb fontos cikkek

- [Ajánlott élő kódolók](encode-recommended-on-premises-live-encoders.md)
- [Egy felhőalapú DVR használata](live-event-cloud-dvr-time-how-to.md)
- [Élő események típusai szolgáltatás összehasonlítása](live-event-types-comparison-reference.md)
- [Állapotok és számlázás](live-event-states-billing-concept.md)
- [Késés](live-event-latency-reference.md)

## <a name="live-streaming-questions"></a>Élő közvetítéssel kapcsolatos kérdések

Tekintse meg az [élő közvetítéssel kapcsolatos kérdések](questions-collection.md#live-streaming) című cikket.
