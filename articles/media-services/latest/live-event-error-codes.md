---
title: Azure Media Services élő események hibakódja
description: Ez a cikk az élő események hibakódait sorolja fel.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: error-reference
ms.date: 03/26/2021
ms.author: inhenkel
ms.openlocfilehash: 7c30649fe3486f812569cb51f609356a6cbfd58f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105627540"
---
# <a name="media-services-live-event-error-codes"></a>Media Services élő események hibakódja

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

A következő táblázatok az [élő események](live-events-outputs-concept.md) hibakódait tartalmazzák.

## <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

Amikor előfizet egy élő esemény [Event Grid](../../event-grid/index.yml) eseményeire, a következő hibák valamelyikét láthatja a [LiveEventConnectionRejected](monitoring/media-services-event-schemas.md\#liveeventconnectionrejected) eseményből.
> [!div class="mx-tdCol2BreakAll"]
>| Hiba | Tájékoztatás |
>|--|--|
>|**MPE_RTMP_APPID_AUTH_FAILURE** ||
>|Leírás | Helytelen betöltési URL-cím |
>|Javasolt megoldás| A APPID egy GUID-jogkivonat az RTMP betöltési URL-címében. Győződjön meg arról, hogy az a betöltési URL-cím megegyezik az API-ból |
>|**MPE_INGEST_ENCODER_CONNECTION_DENIED** ||
>| Leírás |A kódoló IP-címe nem szerepel a konfigurált IP-engedélyezési listán. |
>| Javasolt megoldás| Győződjön meg arról, hogy a kódoló IP-címe az IP-engedélyezési listán van. A megfelelő érték megadásához használjon online eszközt, például *whoismyip* vagy *CIDR-számológépet* .  Győződjön meg arról, hogy a kódoló a tényleges élő esemény előtt is elérheti a kiszolgálót. |
>|**MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED** ||
>| Leírás|Az RTMP-kódoló nem küldte el a `setDataFrame` parancsot. |
>| Javasolt megoldás|A legtöbb kereskedelmi kódoló stream-metaadatokat küld. Előfordulhat, hogy egy olyan kódolónál, amely egyetlen bitrátát küld, a probléma nem jelent problémát. A LiveEvent képes kiszámítani a bejövő sávszélességet, ha az adatfolyam metaadatainak hiányoznak.  A PassThru-csatornák vagy a kettős leküldéses forgatókönyvek esetében a betöltési URL-cím mezőben a "videodatarate" és a "audiodatarate" lekérdezési karakterláncot is megpróbálhatja hozzáfűzni. A közelítő érték működhet. Az egység kbit-ban van. Például:  `rtmp://hostname:1935/live/GUID_APPID/streamname?videodatarate=5000&audiodatarate=192` |
>|**MPE_INGEST_CODEC_NOT_SUPPORTED** ||
>| Leírás|A megadott kodek nem támogatott.|
>| Javasolt megoldás| A LiveEvent nem támogatott kodeket kapott. Például egy RTMP betöltését, a LiveEvent nem AVC videó kodeket kapott.  A kódoló beállításkészletének ellenőrzését. |
>|**MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED** ||
>| Leírás |A Media Description információ nem érkezett meg a tényleges adathordozó-adatok kézbesítése előtt. |
>| Javasolt megoldás|A LiveEvent nem kapja meg az adatfolyam-leírást (fejléc vagy FLV-címke) a kódolóból. Ez egy protokoll megsértése. Forduljon a kódoló gyártójához. |
>|**MPE_INGEST_MEDIA_QUALITIES_EXCEEDED** ||
>| Leírás|A hang-vagy képtípushoz tartozó minőségek száma túllépte a maximálisan megengedett korlátot. |
>| Javasolt megoldás|Ha az élő esemény mód Live Encoding, a kódolónak a videó és hang egyetlen bitrátát kell leküldeni.  Vegye figyelembe, hogy az azonos sávszélességű redundáns leküldés engedélyezett. Ellenőrizze a kódoló előre megadott vagy kimeneti beállításait, és győződjön meg róla, hogy a kimenet egyetlen sávszélességű adatfolyamot eredményez. |
>|**MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED** ||
>| Leírás|Egy élő esemény vagy csatorna szolgáltatás teljes bejövő bitrátája túllépte a maximálisan megengedett korlátot. |
>| Javasolt megoldás|A kódoló túllépte a maximális bejövő sávszélességet. Ez a korlát összesíti az összes bejövő adatokat a közreműködő kódolótól. A Bitsebesség csökkentése érdekében a kódoló előre beállított vagy kimeneti beállításait is megvizsgálhatja. |
>|**MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID** ||
>| Leírás|A videó-vagy hangFLVTag időbélyegzője érvénytelen az RTMP-kódolóból. |
>| Javasolt megoldás|Elavult. |
>|**MPE_INGEST_FRAMERATE_EXCEEDED** ||
>| Leírás|A bejövő kódoló által betöltött adatfolyamok a frame díjszabása túllépte az élő események/csatornák kódolásához megengedett maximális 30 fps-ot. |
>| Javasolt megoldás|A kódoló beállításkészletének megadásával csökkentheti a képkockák sebességét az 36 fps alatt. |
>|**MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED** ||
>| Leírás|A bejövő kódoló által betöltött adatfolyamok meghaladták a következő engedélyezett felbontásokat: 1920x1088 az élő események/csatornák kódolásához, valamint a 4096 x 2160 a továbbított élő eseményekhez/csatornákhoz. |
>| Javasolt megoldás|A kódoló beállításkészletének megadásával csökkentse a videó felbontását, hogy ne lépje túl a korlátot. |
>|**MPE_INGEST_RTMP_TOO_LARGE_UNPROCESSED_FLV** |
>| Leírás|Az élő esemény nagy mennyiségű hanganyagot kapott egyszerre, vagy nagy mennyiségű videó-adatkeret nélkül. Leválasztottuk a kódolót, hogy a megfelelő adatokkal próbálkozzon újra. |
>| Javasolt megoldás|Győződjön meg arról, hogy a kódoló elküld egy kulcstárolót minden kulcstároló-intervallumhoz (GOP).  Engedélyezze a beállításokat, például az "állandó bitrátát (CBR)" vagy a "kulcstartó igazítása" lehetőséget. Előfordulhat, hogy a közreműködő kódoló alaphelyzetbe állítása segíthet. Ha nem segít, forduljon a kódoló forgalmazójához. |

## <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

A következő hibák valamelyikét láthatja a [LiveEventEncoderDisconnected](monitoring/media-services-event-schemas.md\#liveeventencoderdisconnected) eseményből.

> [!div class="mx-tdCol2BreakAll"]
>| Hiba | Tájékoztatás |
>|--|--|
>|**MPE_RTMP_SESSION_IDLE_TIMEOUT** |
>| Leírás|Az RTMP-munkamenet időtúllépést követően időtúllépést követett el az engedélyezett időkorlát után. |
>|Javasolt megoldás|Ez általában akkor fordul elő, ha egy kódoló nem fogadja el a bemeneti adatcsatornát, így a munkamenet üresjáratba kerül, mert nincs leküldhető adat. Ellenőrizze, hogy a kódoló vagy a bemeneti csatorna állapota kifogástalan állapotban van-e. |
>|**MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID** |
>|Leírás| A videó-vagy hang-FLVTag időbélyegzője érvénytelen az RTMP-kódolótól. |
>| Javasolt megoldás| Elavult. |
>|**MPE_CAPACITY_LIMIT_REACHED** |
>| Leírás|A kódoló túl gyorsan küldi az adatokat. |
>| Javasolt megoldás|Ez akkor történik meg, amikor a kódoló rövid idő alatt kirobban a töredékek nagy készletét.  Ez elméletileg akkor fordulhat elő, ha a kódoló hálózati probléma miatt nem tud leküldeni egy adott adatforrást, és a hálózat elérhetővé válik. A kódoló naplójának vagy rendszernaplójának okának megkeresése. |
>|**Ismeretlen hibakódok** |
>| Leírás| Ezek a hibakódok a kivonatoló leképezésben a memóriából a duplikált bejegyzések között lehetnek. Ez akkor fordulhat elő, ha a kódoló rövid idő alatt nagy mennyiségű töredéket küld el.  Ez akkor is előfordulhat, ha a kódoló hálózati hiba miatt nem tudott adatokat leküldeni, majd az összes késleltetett töredéket elküldi, amikor a hálózat elérhetővé válik. |
>|Javasolt megoldás| Keresse meg a kódoló naplóit.|

## <a name="other-error-codes"></a>Egyéb hibakódok

> [!div class="mx-tdCol2BreakAll"]
>| Hiba | Tájékoztatás |Elutasított/leválasztott esemény|
>|--|--|--|
>|**ERROR_END_OF_MEDIA** ||Yes|
>| Leírás|Ez általános hiba. ||
>|Javasolt megoldás| Nincsenek.||
>|**MPI_SYSTEM_MAINTENANCE** ||Yes|
>| Leírás|A kódoló a szolgáltatás frissítése vagy a rendszer karbantartása miatt megszakadt. ||
>|Javasolt megoldás|Győződjön meg arról, hogy a kódoló engedélyezi az automatikus összekapcsolást. Lehetővé teszi, hogy a kódoló újra csatlakozhasson a nem karbantartás alatt álló redundáns élő esemény-végponthoz. ||
>|**MPE_BAD_URL_SYNTAX** ||Yes|
>| Leírás|A betöltési URL-cím formátuma helytelen. ||
>|Javasolt megoldás|Győződjön meg arról, hogy a betöltési URL-cím megfelelően van formázva. RTMP esetén a következőnek kell lennie: `rtmp[s]://hostname:port/live/GUID_APPID/streamname` ||
>|**MPE_CLIENT_TERMINATED_SESSION** ||Yes|
>| Leírás|A kódoló leválasztotta a munkamenetet.  ||
>|Javasolt megoldás|Ez nem hiba. A kódoló kezdeményezte a leválasztást, beleértve a biztonságos leválasztást is. Ha ez egy váratlan kapcsolat, ellenőrizze a kódoló naplóit. |
>|**MPE_INGEST_BITRATE_NOT_MATCH** ||Nem|
>| Leírás|A bejövő adatforgalom nem felel meg a várt bitráta értéknek. ||
>|Javasolt megoldás|Ez egy figyelmeztetés, amely akkor fordul elő, ha a bejövő adatforgalom túl lassú vagy gyors. A kódoló naplójának vagy rendszernaplójának beolvasása.||
>|**MPE_INGEST_DISCONTINUITY** ||Nem|
>| Leírás| Discontinuty van a bejövő adatsorokban.||
>|Javasolt megoldás| Ez egy figyelmeztetés arról, hogy a kódoló hálózati hiba vagy rendszererőforrás-probléma miatt elveszíti az adatvesztést. Keresse meg a kódoló naplóját vagy a rendszernaplót. Figyelje a rendszererőforrást (processzor, memória vagy hálózat) is. Ha a rendszer CPU-értéke túl magas, próbálja meg csökkenteni a bitrátát, vagy használja a rendszergrafikus kártyán a H/W kódoló kapcsolót.||

## <a name="see-also"></a>Lásd még

[Adatfolyam-végpont (forrás) hibakódok](streaming-endpoint-error-codes.md)

## <a name="next-steps"></a>Következő lépések

[Oktatóanyag: élő közvetítés Media Services](stream-live-tutorial-with-api.md)
