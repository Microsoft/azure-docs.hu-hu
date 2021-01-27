---
title: Media Services terminológia és fogalmak
description: Ismerkedjen meg a Azure Media Services terminológiával és fogalmakkal.
services: media-servicesgit
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: b425cd9268b336a8926e4fad9cb1f288f4fe3e87
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897917"
---
# <a name="media-services-terminology-and-concepts"></a>Media Services terminológia és fogalmak

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ez a témakör rövid áttekintést nyújt Azure Media Services terminológiáról és fogalmakról. A cikk a cikkekre mutató hivatkozásokat is tartalmaz, amelyek részletesen ismertetik a Media Services v3 fogalmakat és funkciókat.

A fejlesztés megkezdése előtt tekintse át az alábbi témakörökben ismertetett alapvető fogalmakat.

> [!NOTE]
> Jelenleg a [Azure Portal](https://portal.azure.com/) használható a következőhöz: Media Services v3 [élő események](live-events-outputs-concept.md), View (nem felügyelt) v3- [eszközök](assets-concept.md)kezelése, valamint az API-k [elérésére vonatkozó információk beszerzése](./access-api-howto.md).
> Az összes többi felügyeleti feladathoz (például [átalakításokhoz, feladatokhoz](transforms-jobs-concept.md) és [tartalmak védelméhez](content-protection-overview.md)) használja a [REST API](/rest/api/media/accountfilters), a [CLI](/cli/azure/ams)vagy a támogatott [SDK](media-services-apis-overview.md#sdks)-k egyikét.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="media-services-v3-terminology"></a>Media Services v3 terminológia

|Időszak|Leírás|
|---|---|
|Élő esemény|Egy **élő esemény** a betöltéshez, az átkódoláshoz (opcionálisan) és a videó-, hang-és valós idejű metaadatokból álló élő streamek csomagolásához szükséges folyamatokat jelöli.<br/><br/>Media Services v2 API-kból áttelepítést igénylő ügyfelek esetében az **élő esemény** a **Channel** entitást a v2-ben váltja fel. További információ: [Migrálás v2-ről v3](migrate-v-2-v-3-migration-introduction.md)-ra.|
|Folyamatos átviteli végpont/csomagolás/forrás|A **folyamatos átviteli végpontok** egy dinamikus (igény szerinti) csomagolási és forrás-szolgáltatást jelentenek, amely az élő és igény szerinti tartalmat közvetlenül az ügyfél-lejátszó alkalmazásnak kézbesíti. A szolgáltatás a Common Streaming Media protokollok (HLS vagy DASH) egyikét használja. Emellett az adatfolyam- **végpont** dinamikus (igény szerinti) titkosítást biztosít az iparági vezető digitális jogkezelési rendszerek (DRMs) számára.<br/><br/>A Media Streaming Industry szolgáltatásban ezt a szolgáltatást általában **csomagolónak** vagy **forrásnak** nevezzük.  Az iparág egyéb általános használati feltételei közé tartozik például a JITP (Just-in-time-becsomagolás) vagy a JITE (Just-in-time-Encryption).

## <a name="media-services-v3-concepts"></a>Media Services v3 fogalmak

|Alapelvek|Description|Hivatkozások|
|---|---|---|
|Eszközök és tartalom feltöltése|Az Azure-beli médiatartalmak kezelésének, titkosításának, kódolásának, elemzésének és továbbításának megkezdéséhez létre kell hoznia egy Media Services fiókot, és fel kell töltenie a digitális fájlokat az **eszközökbe**.|[Felhőbe történő feltöltés és tárolás](storage-account-concept.md)<br/><br/>[Eszközök koncepciója](assets-concept.md)|
|Tartalom kódolása|Miután feltölti a kiváló minőségű digitális médiafájlokat az eszközökbe, kódolhatja azokat formátumokba, amelyek számos böngészőben és eszközön játszhatók le. <br/><br/>Az Media Services v3 kódoláshoz **átalakításokat** és **feladatokat** kell létrehoznia.|[Átalakítások és feladatok](transforms-jobs-concept.md)<br/><br/>[Kódolás Media Services](encoding-concept.md)|
|Tartalomelemzés (Video Indexer)|A Media Services v3 lehetővé teszi a videó-és hangfájlokból származó elemzések kinyerését Media Services v3-es beállításkészlet használatával. Ha Media Services v3-es előkészletekből szeretné elemezni a tartalmat, létre kell hoznia **átalakításokat** és **feladatokat**.<br/><br/>Ha részletesebb információkra van szüksége, használja a [video Indexer](../video-indexer/index.yml) közvetlenül.|[Videó-és hangfájlok elemzése](analyzing-video-audio-files-concept.md)|
|Csomagolás és kézbesítés|A tartalom kódolása után igénybe veheti a **dinamikus csomagolás** előnyeit. Media Services a **folyamatos átviteli végpont** az a dinamikus csomagolási szolgáltatás, amellyel a médiatartalom kézbesíthető az ügyfélszámítógépek számára. Ahhoz, hogy a kimeneti eszközön a videók elérhetők legyenek az ügyfelek számára a lejátszáshoz, létre kell hoznia egy **adatfolyam-keresőt** , majd streaming URL-címeket kell létrehoznia. <br/><br/>A **folyamatos átviteli lokátor** létrehozásakor az eszköz neve mellett meg kell adnia a **folyamatos átviteli szabályzatot** is. A **folyamatos átviteli szabályzatok** lehetővé teszik a folyamatos átviteli protokollok és a titkosítási beállítások (ha vannak) definiálását a **streaming-lokátorok** számára. A dinamikus csomagolás a tartalom élő vagy igény szerinti továbbítására szolgál. <br/><br/>Media Services **dinamikus jegyzékfájlok** használatával csak a videó egy adott kiadatását vagy alklipeit továbbíthatja.|[Dinamikus csomagolás](dynamic-packaging-overview.md)<br/><br/>[Folyamatos átviteli végpontok](streaming-endpoint-concept.md)<br/><br/>[Folyamatos átviteli lokátorok](streaming-locators-concept.md)<br/><br/>[Folyamatos átviteli házirendek](streaming-policy-concept.md)<br/><br/>[Dinamikus jegyzékek](filters-dynamic-manifest-overview.md)<br/><br/>[Szűrők](filters-concept.md)|
|Tartalomvédelem|A Media Services használatával dinamikusan titkosíthatja az élő és igény szerinti tartalmat Advanced Encryption Standard (AES-128) vagy/és a három fő DRM-rendszer valamelyikével: Microsoft PlayReady, Google Widevine és Apple FairPlay. A Media Services egy szolgáltatást is biztosít az AES-kulcsok és a DRM (PlayReady, Widevine és FairPlay) licencek továbbítására a hitelesítő ügyfelek számára. <br/><br/>Ha az adatfolyamban titkosítási beállításokat ad meg, hozza létre a **tartalmi kulcs házirendjét** , és társítsa azt a **folyamatos átviteli lokátorhoz**. A **tartalmi kulcs házirendje** lehetővé teszi annak konfigurálását, hogy a rendszer hogyan továbbítsa a tartalmi kulcsot a végfelhasználók számára.<br/><br/> Próbálja meg újból felhasználni a szabályzatokat, amikor ugyanazok a beállítások szükségesek.| [Tartalmi kulcs házirendjei](content-key-policy-concept.md)<br/><br/>[Tartalomvédelem](content-protection-overview.md)|
|Live streaming (Élő adatfolyam)|A Media Services lehetővé teszi, hogy élő eseményeket nyújtson az ügyfeleknek az Azure-felhőben. Az **élő események** az élő videóadatok betöltését és feldolgozását végzik. Ha **élő eseményt** hoz létre, a rendszer létrehoz egy bemeneti végpontot, amelynek használatával élő jeleket küldhet egy távoli kódolóból. Ha a stream az **élő eseménybe** áramlik, megkezdheti a folyamatos átviteli eseményt egy **eszköz**, egy **élő kimenet** és a **folyamatos átviteli lokátor** létrehozásával. Az **élő kimenet** archiválja a streamet az objektumba **, és** elérhetővé teszi a nézők számára a **folyamatos átviteli végponton** keresztül. Egy élő esemény lehet egy *átmenő* (egy helyszíni élő kódoló több bitrátás streamet küld) vagy *élő kódolást* (a helyszíni élő kódoló egyetlen sávszélességű adatfolyamot küld). |[Élő közvetítés – áttekintés](live-streaming-overview.md)<br/><br/>[Élő események és élő kimenetek](live-events-outputs-concept.md)|
|Figyelés Event Grid|A feladatok előrehaladásának megtekintéséhez használja a **Event Grid**. A Media Services az élő események típusát is kibocsátja. Az Event Grid segítségével az alkalmazások figyelhetik gyakorlatilag az összes Azure-szolgáltatásból és az egyéni forrásokból származó eseményeket, és reagálhatnak azokra. |[Event Grid-események kezelése](reacting-to-media-services-events.md)<br/><br/>[Sémák](media-services-event-schemas.md)|
|Figyelés Azure Monitor|Figyelje a metrikákat és a diagnosztikai naplókat, amelyek segítségével megismerheti, hogy az alkalmazások hogyan működnek együtt a Azure Monitorokkal.|[Metrikák és diagnosztikai naplók](media-services-metrics-diagnostic-logs.md)<br/><br/>[Diagnosztikai naplók sémái](media-services-diagnostic-logs-schema.md)|
|Lejátszóügyfelek|A Azure Media Player használatával a különböző böngészők és eszközök Media Services által továbbított médiatartalom lejátszását is elvégezheti. A Azure Media Player az iparági szabványokat, például a HTML5-t, a Media Source Extensions (MSE) és a titkosított adathordozó-bővítményeket (EME) használja a dúsított Adaptív átviteli élmény biztosításához. |[Az Azure Media Player áttekintése](use-azure-media-player.md)|

## <a name="ask-questions-give-feedback-get-updates"></a>Kérdések feltevése, visszajelzés küldése, frissítések beszerzése

Tekintse meg a [Azure Media Services közösségi](media-services-community.md) cikket, amely különböző módokon jelenítheti meg a kérdéseket, visszajelzéseket küldhet, és frissítéseket kaphat a Media Servicesról.

## <a name="next-steps"></a>Következő lépések

* [Távoli fájl kódolása és videó streamelése – REST](stream-files-tutorial-with-rest.md)
* [Feltöltött fájl kódolása és videó streamelése – .NET](stream-files-tutorial-with-api.md)
* [Élő stream – .NET](stream-live-tutorial-with-api.md)
* [Videó elemzése – .NET](analyze-videos-tutorial-with-api.md)
* [AES-128 dinamikus titkosítás – .NET](protect-with-aes128.md)
* [Dinamikus titkosítás a multi-DRM-.NET használatával](protect-with-drm.md)