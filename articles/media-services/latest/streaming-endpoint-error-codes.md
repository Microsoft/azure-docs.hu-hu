---
title: Azure Media Services csomagolási és származási hibák
description: Ez a témakör a Azure Media Services streaming Endpoint (orgin) szolgáltatástól kapott hibákat ismerteti.
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
ms.date: 05/07/2019
ms.author: inhenkel
ms.openlocfilehash: 05ef67e3caafe2c10d02ee4ca2e3248b7ab6c188
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106120896"
---
# <a name="streaming-endpoint-origin-errors"></a>Streamvégpont (forrás) hibái

Ez a témakör a Azure Media Services [streaming Endpoint szolgáltatástól](streaming-endpoint-concept.md)kapott hibákat ismerteti.

## <a name="400-bad-request"></a>400 Hibás kérés

A kérelem érvénytelen információkat tartalmaz, és a rendszer elutasítja ezeket a hibakódokat, és az alábbi okok egyike miatt:

|Hibakód|Hexadecimális érték |Hibaleírás|
|---|---|---|
|MPE_BAD_URL_SYNTAX |0x80890201|URL-szintaxis vagy formázási hiba. Ilyenek például az érvénytelen típusú, érvénytelen töredék vagy érvénytelen számú kérelmek. |
|MPE_ENC_ENCRYPTION_NOT_SPECIFIED_IN_URL |0x8088024C|A kérelem nem tartalmaz titkosítási címkét az URL-címben. A CMAF-kérelmekhez titkosítási címke szükséges az URL-címben. Az egynél több titkosítási típussal konfigurált egyéb protokollokhoz is szükség van a titkosítási címkére. |
|MPE_STORAGE_BAD_URL_SYNTAX |0x808900E9|A kérelemnek a kérés teljesítésére irányuló kérése hibás kérelemmel kapcsolatos hibát jelzett. |

## <a name="403-forbidden"></a>403 – Tiltott

A kérelem a következő okok egyike miatt nem engedélyezett:

|Hibakód|Hexadecimális érték |Hibaleírás|
|---|---|---|
|MPE_STORAGE_AUTHENTICATION_FAILED |0x808900EA|A kérelemnek a kérés teljesítésére vonatkozó kérése sikertelen volt, és hitelesítési hiba történt. Ez akkor fordulhat elő, ha a rendszer elforgatta a tárolási kulcsokat, és a szolgáltatás nem tudta szinkronizálni a tároló kulcsait. <br/><br/>Vegye fel a kapcsolatot az Azure ügyfélszolgálatával a Azure Portal [Súgó és támogatás](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) szolgáltatásával.|
|MPE_STORAGE_INSUFFICIENT_ACCOUNT_PERMISSIONS |0x808900EB |Tárolási művelet hibája, a hozzáférés nem sikerült, mert a fiók engedélyei nem elegendőek. |
|MPE_STORAGE_ACCOUNT_IS_DISABLED |0x808900EC |A kérés teljesítéséhez szükséges kérelem sikertelen volt, mert a Storage-fiók le van tiltva. |
|MPE_STORAGE_AUTHENTICATION_FAILURE |0x808900F3 |Tárolási művelet hibája, a hozzáférés általános hibák miatt nem sikerült. |
|MPE_OUTPUT_FORMAT_BLOCKED |0x80890207 |A kimeneti formátum a StreamingPolicy lévő konfiguráció miatt le van tiltva. |
|MPE_ENC_ENCRYPTION_REQUIRED |0x8088021E |A tartalom titkosítása szükséges, a kimeneti formátumhoz a kézbesítési házirend szükséges. |
|MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY |0x8088024D |A titkosítás nincs beállítva a kézbesítési házirend beállításaiban. |

## <a name="404-not-found"></a>404 Nem található

A művelet olyan erőforráson próbálkozik, amely már nem létezik. Előfordulhat például, hogy az erőforrás már törölve lett.

|Hibakód|Hexadecimális érték |Hibaleírás|
|---|---|---|
|MPE_EGRESS_TRACK_NOT_FOUND |0x80890209 |A kért követés nem található. |
|MPE_RESOURCE_NOT_FOUND |0x808901F9 |A kért erőforrás nem található. |
|MPE_UNAUTHORIZED |0x80890244 |A hozzáférés nem engedélyezett. |
|MPE_EGRESS_TIMESTAMP_NOT_FOUND |0x8089020A |A kért időbélyeg nem található. |
|MPE_EGRESS_FILTER_NOT_FOUND |0x8089020C |A kért dinamikus jegyzékfájl szűrője nem található. |
|MPE_FRAGMENT_BY_INDEX_NOT_FOUND |0x80890252 |A kért töredék-index az érvényes tartományon kívül esik. |
|MPE_LIVE_MEDIA_ENTRIES_NOT_FOUND |0x80890254 |Nem található az élő adathordozó bejegyzései a Moov-puffer beszerzéséhez. |
|MPE_FRAGMENT_TIMESTAMP_NOT_FOUND |0x80890255 |Egy adott műsorszám esetében a kért időpontban nem található a töredék.<br/><br/>Lehet, hogy a töredék nincs a tárolóban. Próbáljon ki egy másik réteget a bemutatóban, amely tartalmazhat töredéket. |
|MPE_MANIFEST_MEDIA_ENTRY_NOT_FOUND |0x80890256 |Nem található a kért bitrátához tartozó adathordozó-bejegyzés a jegyzékfájlban. <br/><br/>Az is lehet, hogy a játékos a jegyzékfájlban nem szereplő bizonyos bitrátát kér.|
|MPE_METADATA_NOT_FOUND |0x80890257 |Nem található bizonyos metaadatok a jegyzékfájlban, vagy nem található a tárterületről való újraindulási érték. |
|MPE_STORAGE_RESOURCE_NOT_FOUND |0x808900ED |Tárolási művelet hibája, az erőforrás nem található. |

## <a name="409-conflict"></a>409 Ütközés

A (z) vagy művelet egy erőforrásához megadott azonosítót egy `PUT` `POST` meglévő erőforrás hozta. A probléma megoldásához használjon másik azonosítót az erőforráshoz.

|Hibakód|Hexadecimális érték |Hibaleírás|
|---|---|---|
|MPE_STORAGE_CONFLICT  |0x808900EE  |Tárolási műveleti hiba, ütközési hiba.  |

## <a name="410"></a>410

|Hibakód|Hexadecimális érték |Hibaleírás|
|---|---|---|
|MPE_FILTER_FORCE_END_LEFT_EDGE_CROSSED_DVR_WINDOW|0x80890263|Élő közvetítés esetén, ha a forceEndTimestamp beállított szűrő igaz értékre van állítva, akkor a kezdő vagy záró időbélyeg a jelenlegi DVR-ablakon kívül esik.|

## <a name="412-precondition-failure"></a>412 előfeltételi hiba

A művelet olyan eTag adott meg, amely eltér a kiszolgálón elérhető verziótól, azaz egy optimista egyidejűségi hiba. Az erőforrás legfrissebb verziójának beolvasása és a kérés eTagjének frissítése után próbálkozzon újra a kéréssel.

|Hibakód|Hexadecimális érték |Hibaleírás|
|---|---|---|
|MPE_FRAGMENT_NOT_READY |0x80890200 |A kért töredék nem áll készen.|
|MPE_STORAGE_PRECONDITION_FAILED| 0x808900EF|Tárolási műveleti hiba, előfeltételi hiba.|

## <a name="415-unsupported-media-type"></a>415 nem támogatott adathordozó-típus

Az ügyfél által eljuttatott hasznos adatok formátuma nem támogatott.

|Hibakód|Hexadecimális érték |Hibaleírás|
|---|---|---|
|MPE_ENC_ALREADY_ENCRYPTED| 0x8088021F| A titkosítás nem alkalmazható a már titkosított tartalomra.|
|MPE_ENC_INVALID_INPUT_ENCRYPTION_FORMAT|0x8088021D |A bemeneti formátum esetében a titkosítás érvénytelen.|
|MPE_INVALID_ASSET_DELIVERY_POLICY_TYPE|0x8088021C| A kézbesítési szabályzat típusa érvénytelen.|
|MPE_ENC_MULTIPLE_SAME_DELIVERY_TYPE|0x8088024E |Az eredeti beállításokat több kimeneti formátum is megoszthatja.|
|MPE_FORMAT_NOT_SUPPORTED|0x80890205|Az adathordozó formátuma vagy típusa nem támogatott. A Media Services például nem támogatja a 64-nál nagyobb minőségi szintű darabszámot. A FLV-videó címkéjében Media Services nem támogatja a több SPS-t és több PPS-t tartalmazó videó-keretet.|
|MPE_INPUT_FORMAT_NOT_SUPPORTED|0x80890218| A kért eszköz bemeneti formátuma nem támogatott. Media Services támogatja a Smooth (élő), az MP4 (VoD) és a progresszív letöltési formátumokat.|
|MPE_OUTPUT_FORMAT_NOT_SUPPORTED|0x8089020D|A kért kimeneti formátum nem támogatott. Media Services támogatja a Smooth, a DASH (CSF, CMAF), a HLS (v3, v4, CMAF) és a progresszív letöltési formátumokat.|
|MPE_ENCRYPTION_NOT_SUPPORTED|0x80890208|A rendszer nem támogatott titkosítási típust észlelt.|
|MPE_MEDIA_TYPE_NOT_SUPPORTED|0x8089020E|A kért adathordozó-típust a kimeneti formátum nem támogatja. A támogatott típusok a következők: video-, hang-vagy "SUBT" alcím.|
|MPE_MEDIA_ENCODING_NOT_SUPPORTED|0x8089020F|A forrás-eszköz adathordozója olyan adathordozó-formátummal lett kódolva, amely nem kompatibilis a kimeneti formátummal.|
|MPE_VIDEO_ENCODING_NOT_SUPPORTED|0x80890210|A forrás eszköz olyan videoformátum szerint lett kódolva, amely nem kompatibilis a kimeneti formátummal. A h. 264, az AVC, a H. 265 (HEVC, hev1 vagy hvc1) támogatott.|
|MPE_AUDIO_ENCODING_NOT_SUPPORTED|0x80890211|A forrás eszköz olyan hangformátummal lett kódolva, amely nem kompatibilis a kimeneti formátummal. A támogatott hangformátumok az AAC, az E-AC3 (DD +), a Dolby DTS.|
|MPE_SOURCE_PROTECTION_CONVERSION_NOT_SUPPORTED|0x80890212|A forrásként védett eszköz nem alakítható át kimeneti formátumra.|
|MPE_OUTPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890213|A kimeneti formátum nem támogatja a védelmi formátumot.|
|MPE_INPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890219|A bemeneti formátum nem támogatja a védelmi formátumot.|
|MPE_INVALID_VIDEO_NAL_UNIT|0x80890231|Érvénytelen video-hangegység, például csak a minta első, AUD lehet.|
|MPE_INVALID_NALU_SIZE|0x80890260|A NAL egység mérete érvénytelen.|
|MPE_INVALID_NALU_LENGTH_FIELD|0x80890261|Érvénytelen az egység hosszának értéke.|
|MPE_FILTER_INVALID|0x80890236|Érvénytelen dinamikus jegyzékfájlok szűrői.|
|MPE_FILTER_VERSION_INVALID|0x80890237|Érvénytelen vagy nem támogatott szűrési verziók.|
|MPE_FILTER_TYPE_INVALID|0x80890238|A szűrő típusa érvénytelen.|
|MPE_FILTER_RANGE_ATTRIBUTE_INVALID|0x80890239|A szűrő érvénytelen tartományt adott meg.|
|MPE_FILTER_TRACK_ATTRIBUTE_INVALID|0x8089023A|Érvénytelen Track attribútum van megadva a szűrőben.|
|MPE_FILTER_PRESENTATION_WINDOW_INVALID|0x8089023B|A szűrő a megjelenítési ablak hosszát határozza meg.|
|MPE_FILTER_LIVE_BACKOFF_INVALID|0x8089023C|Érvénytelen élő visszalépési érték van megadva a szűrőben.|
|MPE_FILTER_MULTIPLE_SAME_TYPE_FILTERS|0x8089023D|A régi szűrők csak egy absTimeInHNS elemet támogatnak.|
|MPE_FILTER_REMOVED_ALL_STREAMS|0x8089023E|A szűrők alkalmazása után a rendszer egyáltalán nem rendelkezik több streamtel.|
|MPE_FILTER_LIVE_BACKOFF_OVER_DVRWINDOW|0x8089023F|Az élő biztonsági mentés a DVR ablakon kívül esik.|
|MPE_FILTER_LIVE_BACKOFF_OVER_PRESENTATION_WINDOW|0x80890262|Az élő vissza érték nagyobb, mint a bemutató ablak.|
|MPE_FILTER_COMPOSITION_FILTER_COUNT_OVER_LIMIT|0x80890246|Meghaladta a tíz (10) maximális engedélyezett alapértelmezett szűrőt.|
|MPE_FILTER_COMPOSITION_MULTIPLE_FIRST_QUALITY_OPERATOR_NOT_ALLOWED|0x80890248|Több első videós minőségi operátor nem engedélyezett a kombinált kérelmek szűrőinek használatakor.|
|MPE_FILTER_FIRST_QUALITY_ATTRIBUTE_INVALID|0x80890249|Az első minőségi bitráta attribútumainak egy (1) értékkel kell rendelkezniük.|
|MPE_HLS_SEGMENT_TOO_LARGE|0x80890243|A HLS-szegmens időtartamának kisebbnek kell lennie a DVR-ablak egy harmadánál, és vissza kell HLS.|
|MPE_KEY_FRAME_INTERVAL_TOO_LARGE|0x808901FE|A töredék időtartamának körülbelül 20 másodpercnél kisebbnek vagy azzal egyenlőnek kell lennie, vagy a bemeneti minőségi szintek nem illeszkednek egymáshoz.|
|MPE_DTS_RESERVEDBOX_EXPECTED|0x80890105|DTS-specifikus hiba, nem található a ReservedBox, ha a DTSSpecficBox a DTS Box elemzése során jelen kell lennie.|
|MPE_DTS_INVALID_CHANNEL_COUNT|0x80890106|DTS-specifikus hiba, nem találhatók csatornák a DTSSpecficBox a DTS Box-elemzés során.|
|MPE_DTS_SAMPLETYPE_MISMATCH|0x80890107|DTS-specifikus hiba, a minta típusa nem egyezik a DTSSpecficBox.|
|MPE_DTS_MULTIASSET_DTSH_MISMATCH|0x80890108|DTS-specifikus hiba: a többtényezős beállítás értéke DTSH, de a minta típusa nem egyezik.|
|MPE_DTS_INVALID_CORESTREAM_SIZE|0x80890109|DTS-specifikus hiba: a Core stream mérete érvénytelen.|
|MPE_DTS_INVALID_SAMPLE_RESOLUTION|0x8089010A|DTS-specifikus hiba, a minta feloldása érvénytelen.|
|MPE_DTS_INVALID_SUBSTREAM_INDEX|0x8089010B|DTS-specifikus hiba, a sub-stream bővítmény indexe érvénytelen.|
|MPE_DTS_INVALID_BLOCK_NUM|0x8089010C|DTS-specifikus hiba, a sub-stream blokk száma érvénytelen.|
|MPE_DTS_INVALID_SAMPLING_FREQUENCE|0x8089010D|DTS-specifikus hiba, a mintavételi gyakoriság érvénytelen.|
|MPE_DTS_INVALID_REFCLOCKCODE|0x8089010E|DTS-specifikus hiba, a hivatkozási órajel kódja az ALStream-bővítményben érvénytelen.|
|MPE_DTS_INVALID_SPEAKERS_REMAP|0x8089010F|DTS-specifikus hiba: a beállított hangszórók száma érvénytelen.|

Titkosítási cikkekért és Példákért lásd:

- [Koncepció: tartalomvédelem](drm-content-protection-concept.md)
- [Koncepció: tartalmi kulcs házirendjei](drm-content-key-policy-concept.md)
- [Koncepció: folyamatos átviteli szabályzatok](streaming-policy-concept.md)
- [Minta: védelem AES-titkosítással](drm-playready-license-template-concept.md)
- [Minta: védelem a DRM-mel](drm-protect-with-drm-tutorial.md)

A szűrési útmutatásért lásd:

- [Koncepció: dinamikus jegyzékfájlok](filters-dynamic-manifest-concept.md)
- [Koncepció: szűrők](filters-concept.md)
- [Minta: szűrők létrehozása REST API-kkal](filters-dynamic-manifest-rest-howto.md)
- [Minta: szűrők létrehozása a .NET-tel](filters-dynamic-manifest-dotnet-how-to.md)
- [Minta: szűrők létrehozása a parancssori felület használatával](filters-dynamic-manifest-cli-how-to.md)

Az élő cikkek és minták esetében lásd:

- [Koncepció: élő közvetítés – áttekintés](stream-live-streaming-concept.md)
- [Koncepció: élő események és élő kimenetek](live-event-outputs-concept.md)
- [Minta: élő közvetítés – oktatóanyag](stream-live-tutorial-with-api.md)

## <a name="416-range-not-satisfiable"></a>416 A tartománykérelem nem teljesíthető

|Hibakód|Hexadecimális érték |Hibaleírás|
|---|---|---|
|MPE_STORAGE_INVALID_RANGE|0x808900F1|Tárolási műveleti hiba, visszaadott http 416 hiba, érvénytelen tartomány.|

## <a name="500-internal-server-error"></a>500 Belső kiszolgálóhiba

A kérelem feldolgozása során Media Services olyan hibába ütközik, amely megakadályozza a feldolgozás folytatását.  

|Hibakód|Hexadecimális érték |Hibaleírás|
|---|---|---|
|MPE_STORAGE_SOCKET_TIMEOUT|0x808900F4|A rendszer a következő WinHTTP-hibakód miatt kapott és fordította: ERROR_WINHTTP_TIMEOUT (0x00002ee2).|
|MPE_STORAGE_SOCKET_CONNECTION_ERROR|0x808900F5|A rendszer a következő WinHTTP-hibakód miatt kapott és fordította: ERROR_WINHTTP_CONNECTION_ERROR (0x00002efe).|
|MPE_STORAGE_SOCKET_NAME_NOT_RESOLVED|0x808900F6|A rendszer a következő WinHTTP-hibakód miatt kapott és fordította: ERROR_WINHTTP_NAME_NOT_RESOLVED (0x00002ee7).|
|MPE_STORAGE_INTERNAL_ERROR|0x808900E6|Tárolási műveleti hiba, a HTTP 500-hibák egyikének általános InternalError.|
|MPE_STORAGE_OPERATION_TIMED_OUT|0x808900E7|Tárolási műveleti hiba, a HTTP 500-hibák egyikének általános OperationTimedOut.|
|MPE_STORAGE_FAILURE|0x808900F2|Tárolási műveleti hiba, egyéb HTTP 500-hibák, mint a InternalError vagy a OperationTimedOut.|

## <a name="503-service-unavailable"></a>503 A szolgáltatás nem érhető el

A kiszolgáló jelenleg nem tud kéréseket fogadni. Ezt a hibát a szolgáltatásnak küldött túl sok kérés okozhatja. A Media Services szabályozási mechanizmusa korlátozza azon alkalmazások erőforrás-használatát, amelyek túl sok kérést küldenek a szolgáltatásnak.

> [!NOTE]
> A hibaüzenetet és a hibakódsztringet megtekintve juthat részletesebb információhoz az 503-as hiba okáról. Ez a hiba nem mindig jelent szabályozást.
> 

|Hibakód|Hexadecimális érték |Hibaleírás|
|---|---|---|
|MPE_STORAGE_SERVER_BUSY|0x808900E8|Tárolási művelet hibája, a HTTP-kiszolgáló foglalt hibája 503.|

## <a name="ask-questions-give-feedback-get-updates"></a>Kérdések feltevése, visszajelzés küldése, frissítések beszerzése

Tekintse meg a [Azure Media Services közösségi](media-services-community.md) cikket, amely különböző módokon jelenítheti meg a kérdéseket, visszajelzéseket küldhet, és frissítéseket kaphat a Media Servicesról.

## <a name="see-also"></a>Lásd még

- [Kódolási hibakódok](/rest/api/media/jobs/get#joberrorcode)
- [Azure Media Services fogalmak](concepts-overview.md)
- [Kvóták és korlátok](limits-quotas-constraints-reference.md)

## <a name="next-steps"></a>Következő lépések

[Példa: hozzáférés ErrorCode és üzenetküldés a ApiException és a .NET használatával](configure-connect-dotnet-howto.md#connect-to-the-net-client)
