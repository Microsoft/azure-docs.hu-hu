---
title: Az Azure IoT Hub eszköz frissítésének biztonsága | Microsoft Docs
description: Ismerje meg, hogy a IoT Hub eszköz frissítése hogyan gondoskodik az eszközök biztonságos frissítéséről.
author: lichris
ms.author: lichris
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub
ms.openlocfilehash: cf05d5f93180db91658d0e94a23359edd5b0f7ad
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663640"
---
# <a name="device-update-security-model"></a>Eszköz frissítése biztonsági modell

A IoT Hub eszköz frissítése biztonságos módszert kínál az eszköz belső vezérlőprogram, lemezképek és alkalmazások frissítéseinek telepítéséhez a IoT-eszközökre. A munkafolyamat egy teljes körű biztonságos csatornát biztosít egy teljes körű felügyeleti modellel, amelyet az eszköz a frissítés megbízhatóságának, módosításának és szándékos állapotának bizonyítására használhat fel.

Az eszköz frissítési munkafolyamatának minden lépése különböző biztonsági funkciókkal és folyamatokkal védett, így biztosítva, hogy a folyamat minden lépése biztonságos handoff hajtson végre a következőre. Az eszköz frissítési ügyfelének azonosítja és megfelelően kezeli az összes törvénytelen frissítési kérelmet. Az ügyfél emellett ellenőrzi, hogy a tartalom megbízható-e, nem módosult-e, és szándékos-e.

## <a name="for-solution-operators"></a>Megoldási operátorok esetén

Ahogy a megoldás-kezelők frissítéseket importálnak az eszközük frissítési példányára, a szolgáltatás feltölti és ellenőrzi a bináris fájlok frissítését, hogy azok ne legyenek módosítva, vagy egy rosszindulatú felhasználó kicserélte őket. Az ellenőrzés után az eszköz frissítési szolgáltatása létrehoz egy belső [frissítési jegyzékfájlt](./update-manifest.md) az importálási jegyzékből és más metaadatokból. Ezt követően az eszköz frissítési szolgáltatása aláírja ezt a frissítési jegyzékfájlt.

Amikor a megoldás kezelője egy eszköz frissítésére vonatkozó kéréseket küld, a védett IoT Hub csatornán továbbítanak egy aláírt üzenetet az eszközre. A kérelem aláírását az eszköz az eszköz frissítési ügynöke által hitelesítettként ellenőrzi. 

Az eredményül kapott bináris letöltéseket a frissítési jegyzékfájl aláírásának ellenőrzése biztosítja. A frissítési jegyzékfájl tartalmazza a bináris fájl kivonatait, így ha a jegyzékfájl megbízható, az eszköz frissítési ügynöke megbízik a kivonatokban, és megfelel a bináris fájljainak. Miután letöltötte és ellenőrizte a frissítési bináris fájlt, azt a rendszer az eszközön lévő telepítőnek adja.

## <a name="for-device-builders"></a>Eszköz-építők számára

Annak biztosítása érdekében, hogy az eszköz frissítési szolgáltatása egyszerű, alacsony teljesítményű eszközökre legyen méretezhető, a biztonsági modell nyers aszimmetrikus kulcsokat és nyers aláírásokat használ. JSON-alapú formátumokat használnak, például JSON webes tokeneket & JSON-webkulcsokat.

### <a name="securing-update-content-via-the-update-manifest"></a>Frissítési tartalom biztonságossá tétele a frissítési jegyzékfájl használatával

A frissítési jegyzékfájl ellenőrzése két aláírás használatával történik. Az aláírások az *aláíró* kulcsokat és a *legfelső szintű* kulcsokat tartalmazó struktúra használatával jönnek létre.

Az eszköz frissítési ügynöke beágyazott nyilvános kulcsokkal rendelkezik, amelyek az összes eszköz Update-kompatibilis eszközhöz használhatók. Ezek a *legfelső szintű* kulcsok. A megfelelő titkos kulcsokat a Microsoft vezérli.

A Microsoft olyan nyilvános/titkos kulcspárt is létrehoz, amely nem szerepel az eszköz frissítési Ügynökében, vagy az eszközön van tárolva. Ez az *aláíró* kulcs.

Ha egy frissítést importál az IoT Hub eszköz frissítésére, és a frissítési jegyzékfájlt a szolgáltatás hozza létre, a szolgáltatás aláírja a jegyzékfájlt az aláíró kulccsal, és magába foglalja az aláíró kulcsot, amelyet egy legfelső szintű kulcs aláír. Amikor a rendszer elküldi a frissítési jegyzékfájlt az eszközre, az eszköz frissítési ügynöke a következő aláírási adatként fogadja el:

1. Maga az aláírás értéke.
2. A #1 létrehozásához használt algoritmus.
3. A #1 létrehozásához használt aláíró kulcs nyilvánoskulcs-információi.
4. A nyilvános aláíró kulcs aláírása #3ban.
5. A #3 létrehozásához használt legfelső szintű kulcs nyilvános kulcsának azonosítója.
6. A #4 létrehozásához használt algoritmus.

Az eszköz frissítési ügynöke a fent meghatározott információk alapján ellenőrzi, hogy a nyilvános aláíró kulcs aláírása a legfelső szintű kulccsal van-e aláírva. Az eszköz frissítési ügynöke ezután ellenőrzi, hogy a frissítési jegyzékfájl aláírása alá van-e írva az aláíró kulcs. Ha minden aláírás helyes, a frissítési jegyzékfájlt az eszköz frissítési ügynöke megbízhatónak tekinti. Mivel a frissítési jegyzékfájl magában foglalja a frissítési fájloknak megfelelő fájlkivonat-kivonatokat, a frissítési fájlok is megtekinthetők, ha a kivonatok megegyeznek.

A root és az aláíró kulcsok lehetővé teszik a Microsoft számára, hogy rendszeres időközönként ellássák az aláíró kulcsot, amely az ajánlott biztonsági eljárás.

### <a name="json-web-signature-jws"></a>JSON webes aláírás (JWS)

A `updateManifestSignature` használatával biztosítható, hogy a-ben található információk `updateManifest` nem lettek illetéktelenül módosítva. A a JSON `updateManifestSignature` -Webkulcsokat használó JSON webes aláírással jön létre, amely lehetővé teszi a források ellenőrzését. Az aláírás egy Base64Url-kódolású karakterlánc, amely a következő három szakaszt ismerteti: ".".  A JSON-kulcsok és-tokenek elemzéséhez és ellenőrzéséhez tekintse meg a jws_util. h segítő módszereit.

A JSON web Signature egy széles körben használt [javasolt IETF szabvány](https://tools.ietf.org/html/rfc7515) a tartalmak JSON-alapú adatstruktúrákkal történő aláírásához. Az adatok integritásának biztosítására az adatok aláírásának ellenőrzésével van lehetőség. További információt a JSON web Signature (JWS) [RFC 7515](https://www.rfc-editor.org/info/rfc7515)-ben találhat.

### <a name="json-web-token"></a>JSON Web Token

A JSON webes jogkivonatok egy nyílt, iparági [szabványnak](https://tools.ietf.org/html/rfc7519) megfelelő módszer, amely a jogcímeket két fél közötti biztonságos ábrázolására kéri.

### <a name="root-keys"></a>Legfelső szintű kulcsok

Minden eszköz-frissítési eszköz tartalmaz legfelső szintű kulcsot. Ezek a kulcsok az összes eszköz-frissítés aláírásának legfelső szintű bizalmi kapcsolata. Az aláírásokat a fenti legfelső szintű kulcsok egyikén kell összekapcsolni, hogy azok megbízhatónak tekintendők.

A legfelső szintű kulcsok az idő múlásával változnak, mivel az aláírási kulcsok rendszeres, biztonsági okokból történő elforgatása megfelelő. Ennek eredményeképpen az eszköz frissítési ügynökének szoftverének frissítenie kell magát a legfelső szintű kulcsokkal. 

### <a name="signatures"></a>Aláírások

Az összes aláírást egy, a legfelső szintű kulcs által aláírt aláíró (nyilvános) kulcs fogja elhelyezni. Az aláírás azonosítja, melyik legfelső szintű kulcsot használta az aláíró kulcs aláírására. 

Az eszköz frissítési ügynökének ellenőriznie kell az aláírásokat úgy, hogy először ellenőrzi, hogy az aláíró (nyilvános) kulcs aláírása megfelelő, érvényes-e, és aláírja-e a jóváhagyott legfelső szintű kulcsok egyikét. Az aláírási kulcs sikeres ellenőrzése után maga az aláírás is érvényesíthető a most megbízható aláírás nyilvános kulcsának használatával.

Az aláíró kulcsok sokkal gyorsabb ütemben vannak elforgatva, mint a legfelső szintű kulcsok, ezért a különböző aláíró kulcsok által aláírt üzeneteket kell elvárnia. 

Az aláíró kulcsok visszavonását az eszköz frissítési szolgáltatása felügyeli, így a felhasználók nem kísérlik meg az aláíró kulcsok gyorsítótárazását. Mindig az aláírást kísérő kulcsot használja.

### <a name="receiving-updates"></a>Frissítések fogadása

Az eszköz frissítési ügynöke által fogadott frissítési kérések egy aláírt frissítési jegyzékfájl (UM) dokumentumot tartalmaznak. Az ügynöknek ellenőriznie kell, hogy az UM aláírása megfelelő és sértetlen-e. Ezt úgy végezheti el, hogy ellenőrzi, hogy az UM-aláírás aláíró kulcsát egy megfelelő legfelső szintű kulccsal aláírta-e. Ha elkészült, az ügynök érvényesíti az egyesített üzenetküldési aláírást az aláíró kulcson.

Az egyesített üzenetküldési aláírás érvényesítése után az eszköz frissítési ügynöke "igazság forrásaként" is megbízhat. Az összes további biztonsági megbízhatóság ebből a forrásból ered. 

Az UM URL-eket és fájlkivonat-tartalmakat tartalmaz a letöltéshez és a telepítéshez. Miután az ügynök letöltött egy bináris frissítést, ellenőriznie kell a frissítést az UM-ben található fájlkivonat-kivonaton. Ez egy tranzitív megbízhatósági modellt biztosít a letöltés ellenőrzéséhez. Ez nem csak azt biztosítja, hogy a tartalom sértetlen (nem módosult), de megerősíti, hogy a letöltött elemek valóban a letöltésre szánták. 

### <a name="securing-the-device"></a>Az eszköz biztonságossá tétele

Fontos annak biztosítása, hogy az eszközön a frissítéssel kapcsolatos biztonsági eszközök megfelelően biztonságosak és védve legyenek az eszközön. Az eszközöket, például a legfelső szintű kulcsokat védeni kell a módosítással szemben. Ezt többféleképpen is megteheti, például biztonsági eszközök (TPM, SGX ENKLÁVÉHOZ, HSM vagy más biztonsági eszközök) használata, vagy akár az eszköz frissítési ügynökének is. Az utóbbi megköveteli, hogy az eszköz frissítési ügynökének kódja digitálisan alá legyen írva, és a rendszer kódja integritásának támogatása engedélyezve legyen az ügynök kódjának rosszindulatú módosításával szembeni védelemhez.

További biztonsági intézkedések is megadhatók, például annak biztosítása, hogy az összetevőről az összetevőbe való handoff biztonságos módon történjen. Például egy adott elkülönített fiók regisztrálása a különböző összetevők futtatásához. A hálózati kommunikációt (például REST API hívásokat) csak a helyi állomásra korlátozza.

**[Következő lépés: További információ arról, hogy az eszköz frissítése hogyan használja az Azure RBAC](.\device-update-control-access.md)**